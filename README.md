#Openvpn#

CloudBridge的主要功能是：给外网用户提供接入sae内网的路由能力，同时提供对该能力的限制。

* 允许外部用户访问sae内网服务；
* 限制用户的特定IP和端口号，以及设置用户带宽配额。
* 支持用户登录中on the fly地即时更新策略。

##接口如下：##


**index.php?action=auth&token=$tonken&username=$accesskey&password=$password**

* 认证接口
* 无post数据
* eg.:  
	
		curl "10.13.144.166?action=auth&token=54445cdcf330065d27948bcdc92b5849&username=testuser137&password=xxxxxxsrewr"
	
* 返回:
	
		{"code":5,"status":"user_err","message":"No such user: testuser137"}
		
		{
			'code' : $code
			'status' : $status
			'message' : $message
		}


**index.php?action=get_session&token=$tonken&username=$accesskey**

* 这个接口是在用户连接上cloudbridge之后调用，用来获取用户的会话信息
* 无post数据
* eg.: 
	
		curl "10.13.144.166?action=get_session&token=54445cdcf330065d27948bcdc92b5849&username=wwml2m014l"
	
* 返回
	
		{"code":0,"status":"success","message":[{"login":"2015-06-15 09:57:31","node":"10.13.144.166:101","cid":8,"remote_addr":"123.125.22.206","peer_addr":"10.10.101.10","tchandle":"1:12","manage_iface":"10.13.144.166:100"}]}
	
* 返回结构	
	
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message':
					{
						'login' : '2015-06-15 09:57:31' ,
						'node' : '10.13.144.166:101' ,
						'cid'  :  8 ,
						'remote_addr' : '123.125.22.206' ,
						'peer_addr' : '10.10.101.10' ,
						'tchandle'  : '1:12' ,
						'manage_iface' : '10.13.144.166:100' ,
					}
		}
	

**index.php?action=get_user_tchandle&token=$tonken&username=$accesskey**
	
* 获取用户的tc_handle,这个接口会在用户连接上cloubridge之后调用，可以得到分配给这个用户的tc handle
* 无post数据
* eg.:
	
		curl "10.13.144.166?action=get_user_tchandle&token=54445cdcf330065d27948bcdc92b5849&username=wwml2m014l"
	
* 返回:
	
		{"code":0,"status":"success","message":"1:12"}
	
* 返回结构:
	
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message' : '1:12' ,
		}
	


**index.php?action=kill_client&token=$tonken&username=$accesskey**

* kill user 的连接，kill之后client会直接退出
* 无post数据
* eg.: 
	
		curl "10.13.144.166?action=kill_client&token=54445cdcf330065d27948bcdc92b5849&username=wwml2m014l"
	
* 返回:
		
		{"code":0,"status":"success"}
		
* 返回结构:
		
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message' : 'errmag' ,  //如果成功没有这个字段，如果错误，这个字段为错误信息
		}
		

**index.php?action=login&token=$tonken&node=$node&cid=$cid&username=$accesskey&remote_addr=$remote_addr&peer_addr=$peer_addr**

* 登陆，登陆之后会在数据库中记录用户的session信息,返回用户的session信息
* 参数说明：
	
		node:  cloudbridge service的ip:port
		cid:   这个是client连到cloudbridge之后cloudbridge为这个client分配的一个id号
		remote_addr: 客户端的IP地址
		peer_addr:  cloudbridge为client分配的一个私有的ip地址
	
* 无post数据
* eg.: 
	
		curl "10.13.144.166?action=login&token=54445cdcf330065d27948bcdc92b5849&username=wwml2m014l&cid=12&remote_addr=10.67.21.208&peer_addr=10.10.101.6&node=10.13.144.166:101"
	
* 返回:
		
		{"code":0,"status":"success","message":{"acl":{"10.13.144.165":{"tcp":[10438]},"10.13.144.144":{"tcp":[10438]},"10.13.144.231":{"tcp":[89,80,5000]}},"bandwidth":15,"tc_handle":"1:11"}}
		
* 返回接口:
		
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message': 
					{
						acl : 
						{
							'10.13.144.165' : {"tcp":[10438]} ,
							'10.13.144.144' : {"tcp":[10438]} , 
							'10.13.144.231' : {"tcp":[89,80,5000]} ,
						}
					},
					'bandwidth' : 15 ,
					'tc_handle' : '1:11' ,
		}
		


**index.php?action=logout&token=$tonken&node=$node&cid=$cid&username=$accesskey&tx=$tx&rx=$rx**

* 退出
* 参数说明:
		
		node:  cloudbridge service的ip:port
		cid:   这个是client连到cloudbridge之后cloudbridge为这个client分配的一个id号
		tx:    流入流量
		rx:    流出流量
		
* 无post数据
* eg.: 
	
		curl "10.13.144.166?action=logout&token=54445cdcf330065d27948bcdc92b5849&node=10.13.144.166:101&username=wwml2m014l&cid=12&tx=10000&rx=10000"
	
* 返回:
		
		{"code":0,"status":"success"}
		
* 返回结构:
		
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message' : 'errmag' ,  //如果成功没有这个字段，如果错误，这个字段为错误信息
		}
		

**index.php?action=get_attr&token=$tonken&username=$accesskey**
	
* 获取用户的信息
* 无post数据
* eg. : 
	
		curl "10.13.144.166?action=get_attr&token=54445cdcf330065d27948bcdc92b5849&username=wwml2m014l"
	
* 返回结果:
		
		{"code":0,"status":"success","message":{"acl":{"10.13.144.165":{"tcp":[10438]},"10.13.144.144":{"tcp":[10438]},"10.13.144.231":{"tcp":[89,80,5000]}},"bandwidth":15,"tc_handle":10,"stats":"OPEN"}}
		
* 返回结构:
		
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message' : 
					{
						'acl' : 
							'10.13.144.165' : {"tcp":[10438]} ,
							'10.13.144.144' : {"tcp":[10438]} ,
							'10.13.144.231' : {"tcp":[89,80,5000]},

					},
					'bandwidth' : 15 ,
					'tc_handle' : 10 ,
					'stats' : 'OPEN',
		}
		

**index.php?action=user_add&token=$tonken&username=$accesskey&bandwidth=$bandwidth**
	
* 添加一个用户
* eg. : 
	
		curl "10.13.144.166?action=user_add&token=54445cdcf330065d27948bcdc92b5849&username=testuser137&bandwidth=10" 
	
* 返回:
		
		{"code":0,"status":"success"}
		
* 返回结构:
		
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message' : 'errmag' ,  //如果成功没有这个字段，如果错误，这个字段为错误信息
		}
		


**index.php?action=user_del&token=$tonken&username=$accesskey**
	
* 删除一个用户
* 无post数据
* eg. : 
	
		curl "10.13.144.166?action=user_del&token=54445cdcf330065d27948bcdc92b5849&username=testuser137"
	
* 返回:
		
		{"code":0,"status":"success"}
		
* 返回结构:
		
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message' : 'errmag' ,  //如果成功没有这个字段，如果错误，这个字段为错误信息
		}
		

**index.php?action=user_modify&token=$tonken&username=$accesskey&bandwidth=$bandwidth**
	
* 修改用户信息
* post 参数
		
		acl="{\"10.67.21.201\":{\"tcp\":[89,80,5001],\"udp\":[89,80,5001]},\"10.67.21.208\":{\"tcp\":[89,80,5001],\"udp\":[89,80,5001]}}"

		{
			'10.67.21.201' : 
				{
					'tcp' : [89,80,5001] ,
					'udp' : [89,80,5001] ,
				}
			'10.67.21.208' : 
				{
					'tcp' : [89,80,5001] ,
					'udp' : [89,80,5001] ,
				}
		}
		
* eg. : 
	
		curl "10.13.144.166?action=user_modify&token=54445cdcf330065d27948bcdc92b5849&username=testuser137&bandwidth=10" -d "acl={\"10.67.21.201\":{\"tcp\":[89,80,5001],\"udp\":[89,80,5001]},\"10.67.21.208\":{\"tcp\":[89,80,5001],\"udp\":[89,80,5001]}}"
	
* 返回:
		
		{"code":0,"status":"success"}
		
* 返回结构:
		
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message' : 'errmag' ,  //如果成功没有这个字段，如果错误，这个字段为错误信息
		}



**index.php?action=acl_edit&token=$tonken&username=$accesskey&op=$op**
	
* 修改acl策略
* 参数说明:
	* op 是操作参数说明，可以是add或者del，add表示添加acl，del表示删除acl
	* post 参数
		
			acl="{\"10.67.21.201\":{\"tcp\":[89,80,5001],\"udp\":[89,80,5001]},\"10.67.21.208\":{\"tcp\":[89,80,5001],\"udp\":[89,80,5001]}}"

			{
				'10.67.21.201' : 
					{
						'tcp' : [89,80,5001] ,
						'udp' : [89,80,5001] ,
					}
				'10.67.21.208' : 
					{
						'tcp' : [89,80,5001] ,
						'udp' : [89,80,5001] ,
					}
			}
		
* 返回:
		
		{"code":0,"status":"success"}
		
* 返回结构:
		
		{
			'code' : 0 ,
			'status' : 'success' ,
			'message' : 'errmag' ,  //如果成功没有这个字段，如果错误，这个字段为错误信息
		}
		
# python

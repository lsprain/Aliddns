# Aliddns
Aliddns for  RouterOS

使用说明：

  1.本程序服务端由GO语言编写，直接放到服务器上进入cmd执行并保持程序一直运行，执行时会在界面输出相关信息，
  默认端口为 :8800 ，支持GET/POST请求！`不需要web服务器环境。`    
  
  2.RouterOS上配置如下（>6.45）：  
  
:local AccessKeyID "XXXXXXXXX"   //xxxx处替换为需要解析的域名AccessKeyID  
:local AccessKeySecret "XXXXX"   //xxxx处替换为需要解析的域名AccessKeySecret  
:local DomainName "XXXXX"  //xxxx处替换为需要解析的域名，如baidu.com  
:local RR "XXX"  //xxxx处替换为需要解析的子域名，如home.baidu.com只需要填home即可  
:local pppoe "XXXX"  //xxxx处替换为网络出口名称，如pppoe-out1  
:local IpAddr [/ip address get [/ip address find interface=$pppoe] address]  
:set IpAddr [:pick $IpAddr 0 ([len $IpAddr] -3)]  
:local aliddns "http://服务器IP:50020/aliddns?AccessKeyID=$AccessKeyID&AccessKeySecret=$AccessKeySecret&RR=$RR&DomainName=$DomainName&IpAddr=$IpAddr"  
:local result [/tool fetch url=("$aliddns")  mode=http http-method=get     as-value output=user];  

:if ($result->"status" = "finished") do={  
:if ($result->"data" = "same") do={  
:log info "当前配置解析地址与公网IP相同，不需要修改!";  
}  
:if ($result->"data" = "updateipsecc") do={  
:log info "更新域名信息成功!";  
:log info "$IpAddr!";  
}  
:if ($result->"data" = "addsubmainsecc") do={  
:log info "增加域名信息成功!";  
}  
}  

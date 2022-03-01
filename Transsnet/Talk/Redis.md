# 地址  

- test: 172.31.32.240:6379


# Key
## populor房间列表排序

- 最终房间排序: room:heat:sort:list
- 5分钟内相加总热度: room:heat:sort:sum:list
- 房间内在线用户数排行榜(用于首页popular排名): content:room:total:user:rank    
<br/>  

>  %s代表时间，格式为: 202201260531
- 礼物 房间热度key: send:gift:room:heat:%s
- 房间内聊天 热度key: room:chat:heat:%s
- 客户端上报 上麦热度key: custom:add:heat:%s


---
title: 电商的快递查询功能
layout: post
guid: 7hMdDdZqK0hK
tags:
   - God
---

薄荷商店在搭建物流信息系统还算顺利，把整个搭建过程中的所思所想写下来，供大家参考。

物流查询系统要保证

1. 信息更新及时，最好以小时为单位更新。（及时）
2. 所有的快递公司都覆盖。（数据一致）
3. 所有的单号都能查到。（查全率高）

## 如何获取快递信息？

1. 去四通一达的官方网站上抓取

   这个想法不太靠谱。各个网站的提供的快递信息格式不一，而且有验证码，要实现的话逻辑巨复杂。

2. 去 kuaidi100 抓取

    kuaidi100 是金蝶旗下的一个产品，估计四通一达的企业解决方案都是金蝶提供的，所以他们能搞到各个物流公司的快递信息。
    
    接口规范，不折腾，免费接口每天可以查询2000次。

    [申请免费接口](http://www.kuaidi100.com/openapi/)
    
## 薄荷商店的折腾过程

使用快递100的免费接口，一切甚好。免费接口抓取次数至多2000/day，业务量增加后，失败陡增。

这时候心里有点小邪恶，要不要换上十几个 IP，轮流去抓数据？

如果这样干的话良心过不去，代码徒增逻辑，也不好看。还是用付费接口吧。

## 快递100的付费接口

1. 一条快递单号仅一毛钱，挺便宜。

2. 快递信息发生变化后，通过回调主动推送快递信息给薄荷的服务器。

## 提交快递单（Ruby 版）

请求：薄荷 => 快递100


    def subscribe(company, number, from_city, to_city, shipment_id)
      options = {
        company: company, # shunfeng,
        number: number,   # 413432413,
        from: from_city,  # 上海
        to:  to_city,     # 北京
        key: SECRET_KEY,  # kuaidi100给的密钥
        parameters: {
          callbackurl: "http://www.boohee.com/shipments/#{shipment_id}" # 回调地址
        }
      }
    
      response = RestClient.post "http://www.kuaidi100.com/poll", { schema: "json", param: options.to_json }
      response = JSON.parse response
      # {"result"=>"true", "returnCode"=>"200", "message"=>"提交成功"}
    end


## 推送快递信息

请求：快递100 => 薄荷

订阅的快递单号若有变化，快递100会回调我们的服务器（地址为 callback 参数的值），返回json。


    {"message"=>"ok",
     "nu"=>"1201140008858",
     "ischeck"=>"1",
     "companytype"=>"yunda",
     "com"=>"yunda",
     "updatetime"=>"2014-04-10 16:54:15",
     "condition"=>"F00",
     "status"=>"200",
     "codenumber"=>"1201140008858",
     "state"=>"3",
     "data"=>
      [{"time"=>"2014-04-10 13:27:57",
        "context"=>"到达：上海浦东新区南汇惠南北公司 由 图片 签收",
        "ftime"=>"2014-04-10 13:27:57"},
       {"time"=>"2014-04-10 13:23:25",
        "context"=>"到达：上海浦东新区南汇惠南北公司 指定：听潮(18016229226) 派送",
        "ftime"=>"2014-04-10 13:23:25"},
       {"time"=>"2014-04-10 13:16:05",
        "context"=>"到达：上海浦东新区南汇惠南北公司 上级站点：上海浦东新区南汇惠南北公司 发往："
        "ftime"=>"2014-04-10 13:16:05"},
       {"time"=>"2014-04-10 06:08:55",
        "context"=>"到达：上海浦东新区南汇惠南北公司",
        "ftime"=>"2014-04-10 06:08:55"},
       {"time"=>"2014-04-10 06:00:42",
        "context"=>"到达：上海浦东分拨中心 发往：上海浦东新区南汇惠南北公司",
        "ftime"=>"2014-04-10 06:00:42"},
       {"time"=>"2014-04-10 04:45:18",
        "context"=>"到达：上海浦东分拨中心 上级站点：",
        "ftime"=>"2014-04-10 04:45:18"},
       {"time"=>"2014-04-10 04:41:57",
        "context"=>"到达：上海浦东分拨中心 上级站点：上海分拨中心",
        "ftime"=>"2014-04-10 04:41:57"},
       {"time"=>"2014-04-10 02:14:59",
        "context"=>"到达：上海分拨中心 发往：上海浦东分拨中心",
        "ftime"=>"2014-04-10 02:14:59"},
       {"time"=>"2014-04-09 23:27:22",
        "context"=>"到达：上海分拨中心 发往：上海浦东分拨中心",
        "ftime"=>"2014-04-09 23:27:22"},
       {"time"=>"2014-04-09 23:19:33",
        "context"=>"到达：上海分拨中心 上级站点：",
        "ftime"=>"2014-04-09 23:19:33"},
       {"time"=>"2014-04-09 23:11:04",
        "context"=>"到达：上海分拨中心 上级站点：上海宝山区公司",
        "ftime"=>"2014-04-09 23:11:04"},
       {"time"=>"2014-04-09 21:53:27",
        "context"=>"到达：上海宝山区公司 已收件",
        "ftime"=>"2014-04-09 21:53:27"},
       {"time"=>"2014-04-09 21:52:34",
        "context"=>"到达：上海宝山区公司 发往：上海浦东分拨中心",
        "ftime"=>"2014-04-09 21:52:34"},
       {"time"=>"2014-04-09 21:00:53",
        "context"=>"到达：上海宝山区公司 已收件",
        "ftime"=>"2014-04-09 21:00:53"}]}   
        


接口还是挺简单，有需要的同学可以试一试。
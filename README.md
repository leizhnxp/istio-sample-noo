# 尝试搞一个比bookinfo更简单的sample

## 缘起

+ bookinfo，乃至整个istio发行包的samples目录都是很棒的
+ 但其实有些东西暗示了一些可能被误用的最佳实践，针对一些特定的feature展示来说略显繁琐

## 内容（或者是指要考虑特性的要素清单）

+ 所有yaml都合并在一个文件当中，需要单独变更调整，可以考虑使用特征label
+ 需要playground namespace而不是default
+ nginx和openresty两个deployment来表示不同version的workload
+ curl没有设置version是故意的，说明内部不纳入流量version切换管理的workload在出站时依然受到相应的VR的约束
+ 两个service，其中一个带nodeport，用来对比和互相替换
+ gateway的hosts用了一个内部的全名称，其实不是必须的，同时需要curl -H予以设置
+ virtualService有两个，其中一个绑定了gateway，是vs对内对外管理的两种最基本形态
+ DestinationRule主要是为了结合sample来表明，应该命名更直观和更符合大框架的意图

## 测试用命令

命令本身很简单curl即可，但有一些注意事项

+ 最好加一个/404之类什么其实都可以，总之就是为了显示错误页，直接有nginx或openresty的清晰版本
  - 当然一开始就grep式的verify思路其实差不多
+ 通过istiogateway进入时候，需要匹配Host header
+ 通过查看对应的service或者istio doc中获取nodeport的方法，以适应发起外部的请求
  - 如果是kind、k3s环境注意多重梦境的的端口expose问题
+ 查看流量比例的效果时候可以seq 100 | xargs -i kubectl exec ... | grep ... | wc -l


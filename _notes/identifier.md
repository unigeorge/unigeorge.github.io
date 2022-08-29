---
layout: note
title: Identifier
categories: [identifier]
description: 一种 identifier 生成规则
keywords: identifier, rowkey, imei, android_id
---

针对移动互联网产品开展数据分析时，需要定义合适的逻辑来进行去重、标记设备等操作。

## rowkey

rowkey 是面向 app 的设备 id，相比 userid，激活后 rowkey 的稳定性更好，不同 app 不同设备的 rowkey 不同，这很适合于留存的应用场景。

rowkey 是连接激活和活跃的纽带，判断一个激活是否在第 n 日留存，就是看和这个激活同一个 rowkey 的活跃中有没有第 n 日活跃。

rowkey 由 identifier 和 app_name 组成，所以若数据中有这 2 个字段，则可以直接拼接得到，否则需要连接到有这2个字段的数据，从而得到 rowkey。


## imei & android_id


imei（International Mobile Equipment Identity，移动设备国际识别码，又称为国际移动设备标识）是手机的唯一识别号码 MAC 地址，网卡的物理地址通常是由网卡生产厂家烧入网卡的 EPROM（一种闪存芯片，通常可以通过程序擦写），它存储的是传输数据时真正赖以标识发出数据的电脑和接收数据的主机的地址。也就是说，在网络底层的物理传输过程中，是通过物理地址来识别主机的，它一般也是全球唯一的。比如，著名的以太网卡，其物理地址是 48 bit（比特位）的整数，如：44-45-53-54-00-00，以机器可读的方式存入主机接口中。以太网地址管理机构（IEEE）将以太网地址，也就是 48 比特的不同组合，分为若干独立的连续地址组，生产以太网网卡的厂家就购买其中一组，具体生产时，逐个将唯一地址赋予以太网卡。形象的说，MAC 地址就如同我们身份证上的身份证号码，具有全球唯一性。

android_id，在设备首次启动时，系统会随机生成一个 64 位的数字，并把这个数字以 16 进制字符串的形式保存下来，这个16进制的字符串就是 ANDROID_ID，当设备被 wipe 后该值会被重置。

## identifier


从手机上获取 IMEI，MACaddress, AndroidID 这三个信息，生成 identifier。

// 1, if has IMEI and MAC\
// IMEI + "##" + MAC

// 2, if only has MAC\
// MAC + "##" + ANDROID_ID


// 3, if only has IMEI
// IMEI + "##" + ANDROID_ID


// 4. if only has android_id
// ANDROID_ID


// 5. if none of the above
// random generated UUID
# 电视频道官方直播源

各电视频道官方（电视台/总台自有平台）的直播源整理。区别于第三方聚合源，这里只收录 broadcaster 官方渠道。

> **重要**：大部分官方源的播放地址是**按请求 IP 动态分配**的，必须在你自己家里（广东联通）调用接口获取，不能直接抄别人抓好的地址用。

## 目录

- [CCTV（央视网官方 API，已验证）](#cctv)
- [CCTV + 卫视（央视频官方 API）](#央视频)
- [江西卫视（电视台直链）](#江西卫视)
- [TVB 新闻台（官方 App API）](#tvb)
- [台湾（官方 YouTube 24小时直播）](#台湾)
- [暂无公开直链的频道](#暂无公开直链)

---

## CCTV

**接口**（央视网 tv.cctv.com 网页播放器自用接口，2026-10-17 已逐个验证）：

```
GET https://vdn.live.cntv.cn/api2/liveHtml5.do?channel=pa://cctv_p2p_hd<频道ID>
```

- 无需登录，普通请求即可返回
- 返回 JS 包裹的 JSON，取 `hls_url` 字段即为 m3u8 地址
- **必须在家庭网络下调用**，接口按 IP 就近分配 CDN（联通 IP 返回联通优化的国内 CDN）
- 延迟约 15~30 秒（HLS 特性）

**频道 ID 对照表**（全部已验证可用）：

| 频道ID | 频道 | 频道ID | 频道 |
|---|---|---|---|
| cctv1 | CCTV-1 综合 | cctv10 | CCTV-10 科教 |
| cctv2 | CCTV-2 财经 | cctv11 | CCTV-11 戏曲 |
| cctv3 | CCTV-3 综艺 | cctv12 | CCTV-12 社会与法 |
| cctv4 | CCTV-4 中文国际 | cctv13 | CCTV-13 新闻 |
| cctv5 | CCTV-5 体育 | cctvchild | CCTV-14 少儿 |
| cctv5plus | CCTV-5+ 体育赛事 | cctv15 | CCTV-15 音乐 |
| cctv6 | CCTV-6 电影 | cctv16 | CCTV-16 4K超高清 |
| cctv7 | CCTV-7 国防军事 | cctv17 | CCTV-17 农业农村 |
| cctv8 | CCTV-8 电视剧 | | |
| cctvjilu | CCTV-9 纪录 | | |

---

## 央视频

**接口**：`POST https://player-api.yangshipin.cn/v1/player/get_live_info`

央视频（总台官方 App）的接口，一次覆盖 **CCTV 全套 + 22 家卫视**，支持 1080p（`defn=fhd`）。

- 需要按文档做签名（AES-128-CBC `cKey` + MD5 `signature`），详见 `docs/yangshipin-api.md`
- 返回的 m3u8 为签名地址，有效期短，每次播放前重新获取
- 仅限中国大陆 IP

**卫视频道 ID 对照表**：

| 卫视 | 频道ID | 卫视 | 频道ID |
|---|---|---|---|
| 北京卫视 | 2000272103 | 湖北卫视 | 2000294503 |
| 天津卫视 | 2019927003 | 湖南卫视 | 2000296203 |
| 河北卫视 | 2000293403 | 江西卫视 | 2000294103 |
| 辽宁卫视 | 2000281303 | 江苏卫视 | 2000295603 |
| 黑龙江卫视 | 2000293903 | 浙江卫视 | 2000295503 |
| 山东卫视 | 2000294803 | 福建东南卫视 | 2000292503 |
| 安徽卫视 | 2000298003 | 广东卫视 | 2000292703 |
| 河南卫视 | 2000296103 | 深圳卫视 | 2000292203 |
| 四川卫视 | 2000295003 | 广西卫视 | 2000294203 |
| 重庆卫视 | 2000297803 | 海南卫视 | 2000291503 |
| 贵州卫视 | 2000293303 | 新疆卫视 | 2000266303 |

CCTV 的频道 ID 见 `docs/yangshipin-api.md`。

> 以下卫视暂无公开的央视频 ID：云南、陕西、甘肃、宁夏、青海、西藏、内蒙古、吉林、山西、厦门、兵团、农林卫视。可改用各台官方 App（湖南→芒果TV、浙江→中国蓝TV、东方→看看新闻、江苏→荔枝新闻、北京→北京时间）。

---

## 江西卫视

江西广播电视台自有域名直链（`m3u/jiangxi.m3u`）：

```
http://local-live.jxtvcn.com.cn/live/tv_jxtv1.m3u8
```

同域名下 `tv_jxtv2`~`tv_jxtv7` 为江西其他频道。文档记录可用，未逐个实测。

---

## TVB

TVB 官方新闻 App 接口（2026-10-17 已验证返回）：

```
GET https://inews-api.tvb.com/news/checkout/live/hd/ott_A_h264?profile=ios   # 無綫新聞台
GET https://inews-api.tvb.com/news/checkout/live/hd/ott_C_h264?profile=ios   # TVB 新闻App第二频道
```

- 返回 TVB 自有 Akamai CDN 的 m3u8，`hdnea` token 绑定请求 IP，有效期约 30 天
- 无需登录，每次从自己 IP 调用即可
- 翡翠台/明珠台无公开直链，官方渠道为 myTV SUPER（需登录+香港IP）

---

## 台湾

各新闻台官方 YouTube 24 小时直播（官方频道，最稳定）：

| 频道 | YouTube 直播 |
|---|---|
| 公视 | https://www.youtube.com/watch?v=ED4QXd5xAco |
| 台视新闻台 | https://www.youtube.com/watch?v=yk2CUjbyyQY |
| 民视新闻台 | https://www.youtube.com/watch?v=XxJKnDLYZz4 |
| 中视新闻台 | https://www.youtube.com/watch?v=XBne4oJGEhE |
| 华视新闻台 | https://www.youtube.com/watch?v=TL8mmew3jb8 |

> 注意：YouTube 视频 ID 会随重播轮换，失效时去各台官方 YouTube 频道找当前直播。另外 YouTube 链接需用 yt-dlp 等工具解析后才能进 IPTV 播放器。

---

## 暂无公开直链

以下频道未找到公开的直接 m3u8，官方渠道为 App/网页播放器：

| 频道 | 官方渠道 |
|---|---|
| 广东珠江/新闻/体育/公共等 | 触电新闻 App（广东广播电视台官方） |
| 惠州电视台 | 官网 www.hzrtv.cn（大概率无公开流） |
| RTHK 港台电视 31/32 | 官网 rthk.hk/tv/dtt31 |
| 澳视 TDM | 官网 tdm.com.mo |

广东本地频道建议继续用 `iptv-sources` 仓库中的广东联通单播源。

---

## 说明

- **延迟**：官方 HLS 源延迟一般 15~30 秒，不存在公开的官方低延迟源。运营商内网组播（如广东电信 RTP）可达秒级，但仅限对应运营商网络内使用。
- **4K**：CCTV 只有 CCTV-16 是真 4K；各卫视官方流多为 720p/1080p。
- **联通网络**：上述公网接口在广东联通下调用会自动分配联通优化的 CDN，无需特殊配置。

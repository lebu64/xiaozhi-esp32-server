# get_news_from_newsnow Plugin News Source Configuration Guide

## Overview

The `get_news_from_newsnow` plugin now supports dynamically configuring news sources through the web management interface, no longer requiring code modifications. Users can configure different news sources for each agent in the control panel.

## Configuration Methods

### 1. Configure via Web Management Interface (Recommended)

1. Log in to the control panel
2. Enter the "Role Configuration" page
3. Select the agent to configure
4. Click the "Edit Functions" button
5. Find the "newsnow news aggregation" plugin in the right parameter configuration area
6. Enter semicolon-separated Chinese names in the "News Source Configuration" field

### 2. Configuration File Method

Configure in `config.yaml`:

```yaml
plugins:
  get_news_from_newsnow:
    url: "https://newsnow.busiyi.world/api/s?id="
    news_sources: "澎湃新闻;百度热搜;财联社;微博;抖音"
```

## News Source Configuration Format

News source configuration uses semicolon-separated Chinese names, format as:

```
Chinese Name 1;Chinese Name 2;Chinese Name 3
```

### Configuration Example

```
澎湃新闻;百度热搜;财联社;微博;抖音;知乎;36氪
```

## Supported News Sources

The plugin supports the following news sources' Chinese names:

- 澎湃新闻 (The Paper)
- 百度热搜 (Baidu Hot Search)
- 财联社 (Cailian Press)
- 微博 (Weibo)
- 抖音 (Douyin)
- 知乎 (Zhihu)
- 36氪 (36Kr)
- 华尔街见闻 (Wall Street CN)
- IT之家 (IT Home)
- 今日头条 (Toutiao)
- 虎扑 (Hupu)
- 哔哩哔哩 (Bilibili)
- 快手 (Kuaishou)
- 雪球 (Xueqiu)
- 格隆汇 (Gelonghui)
- 法布财经 (Fab Finance)
- 金十数据 (Jin10)
- 牛客 (Nowcoder)
- 少数派 (SSPAI)
- 稀土掘金 (Juejin)
- 凤凰网 (Phoenix News)
- 虫部落 (Chongbuluo)
- 联合早报 (Lianhe Zaobao)
- 酷安 (Coolapk)
- 远景论坛 (PCbeta)
- 参考消息 (Cankao Xiaoxi)
- 卫星通讯社 (Sputnik)
- 百度贴吧 (Baidu Tieba)
- 靠谱新闻 (Kaopu News)
- And more...

## Default Configuration

If no news sources are configured, the plugin will use the following default configuration:

```
澎湃新闻;百度热搜;财联社
```

## Usage Instructions

1. **Configure News Sources**: Set Chinese names of news sources in the web interface or configuration file, separated by semicolons
2. **Call Plugin**: Users can say "播报新闻" or "获取新闻" (Report news or Get news)
3. **Specify News Source**: Users can say "播报澎湃新闻" or "获取百度热搜" (Report The Paper news or Get Baidu hot search)
4. **Get Details**: Users can say "详细介绍这条新闻" (Provide detailed introduction of this news)

## How It Works

1. Plugin accepts Chinese names as parameters (e.g., "澎湃新闻")
2. Based on the configured news source list, converts Chinese names to corresponding English IDs (e.g., "thepaper")
3. Uses English IDs to call API to get news data
4. Returns news content to users

## Important Notes

1. Configured Chinese names must exactly match the names defined in CHANNEL_MAP
2. After configuration changes, service needs to be restarted or configuration reloaded
3. If configured news sources are invalid, plugin will automatically use default news sources
4. Use English semicolons (;) to separate multiple news sources, do not use Chinese semicolons (；)

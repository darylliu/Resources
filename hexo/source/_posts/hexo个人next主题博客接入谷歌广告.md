---
title: hexo个人next主题博客接入谷歌广告
date: 2019-04-12 10:43:23
tags: [hexo,next,adsense]
categories: 杂谈
---

## 前言

个人博客搭了大概也有大半年的时间，陆陆续续更新了一些文章，虽然每天整个网站的浏览量不是特别高，但是也希望能够引入网站的流量变现（蚊子腿再小也是肉啊...），于是自己查阅了相关的资料，整理了一份完整的，接入谷歌广告联盟google adsense的一套完整流程，供大家参考～

<!--more-->

## 准备环境

* 使用hexo的next主题搭建博客
* 已翻墙，可以登录google adsense网站(本人是在[搬瓦工](https://banwagong.cn/)上购买了一年的服务器，自己搭了了一套ShadowSocks环境)

## 注册账号

注册账号流程比较简单，入口在这里：[Google Adsense](https://www.google.com/adsense/)

## 添加广告代码

注册账号完成之后，需要将谷歌提供给你的一份代码添加到你网站的中的	&lt;head&gt;标记中，在你添加完成之后，点击确认，谷歌会到你的网站上进行核查和验收。

对于使用hexo的next主题博客的同学，只要将谷歌提供的代码
```
<script async src="http://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<script>
(adsbygoogle = window.adsbygoogle || []).push({
google_ad_client: "ca-pub-123456789",
enable_page_level_ads: true
});
</script>
```
放到*\themes\next\layout\_partials\head.swig * 任意一个位置(本人放在了最后)即可，在给谷歌确认之前，可以自己先使用浏览器进行验证：
打开自己的网站，点击右键，查看源码，搜索一下是否已经成功添加上述代码。
确认无误之后，在google adsense上点击确认，开始验证，一般没问题的话几分钟就会出结果，有问题的话要等待一段时间。

根据我个人申请的流程，总结了一些经验，供大家参考：
1. 网站文章不要太少，至少大于10篇博客以上。
2. 网站上架半年以上，如果是新申请的网站，一般不容易过审。(这里建议大家有接入google adsense的新站长，等网站稍微成熟点再去申请，本人第一次申请就因为建站时间过短，还不到3个月被拒)

## 个性化配置广告位

待通过审核之后，就可以开始考虑在自己的网站上进行广告位置的筛选和设计了，目前google adsense主要提供了自动广告和广告单元两种形式的广告添加方式。
#### 自动广告
自动广告是google adsense近来提供的一种广告形式，它能够通过分析你的博客布局结构，自定义的在你的网站中插入合适的广告，无论是内容，还是广告尺寸，都是完全契合网站内容本身的，算是一种比较高质量的广告。

但是根据我的使用经验，这种广告投放的几率比较小，往往好几篇文章或页面才会投放一个广告内容，效率比较低(唯一的好处是，如果你的网站支持移动端查看的话，会自动投放移动端自适应的广告)

具体的代码插入方法，其实就是上述的用来检验的代码，一旦上述代码审核通过，其实已经自动接入了google adsense的自动广告。

#### 广告单元
为了能够最高效的利用自己博客的广告位，adsense还提供了三种固定广告位
1. 文字广告和展示广告(即侧边栏，评论区之类的固定广告位)
2. 信息流广告(插入在信息流内容的广告位置)
3. 文章内嵌广告(主要是插入在每篇文章内部的开始，中间，结尾部分，展示次数比较多，强烈推荐)

由于本人的是博客网站，所以第二种信息流广告没有投入使用，这里主要使用了第一种和第三种。

具体的操作流程是，在网站上，选择广告单元->新建广告位->选择对应的广告类型->生成对应的广告代码。

这里，本人根据个人经验，提供几种针对hexo的next主题广告代码位置的插入。

＋ 插入评论区：将代码插入*\themes\next\layout\_partials\comments.swig *中的末尾即可。 
＋ 插入侧边栏：将代码插入*\themes\next\layout\_macro\sidebar.swig *文件中&lt;div class="sidebar-inner"&gt; &lt;/div&gt;的最下侧即可

```
<div class="sidebar-inner">
...
...
...
...
      {% if theme.sidebar.b2t %}
        <div class="back-to-top">
          <i class="fa fa-arrow-up"></i>
          {% if theme.sidebar.scrollpercent %}
            <span id="scrollpercent"><span>0</span>%</span>
          {% endif %}
        </div>
      {% endif %}
      <script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
      <!-- 侧边栏广告 -->
      <ins class="adsbygoogle"
           style="display:block"
           data-ad-client="ca-pub-1219606132354870"
           data-ad-slot="1362622054"
           data-ad-format="auto"
           data-full-width-responsive="true"></ins>
      <script>
      (adsbygoogle = window.adsbygoogle || []).push({});
      </script>
</div>
```

＋ 插入文章头部：在*\themes\next\layout\_custom\post.swig * 目录下，新建google_adsense.swig，并将google提供的广告代码放入其中，然后将
```
{% include '../_custom/google_adsense.swig' %}
```
插入*\themes\next\layout\_macro\post.swig *文件中
```
<div class="post-body{% if theme.han %} han-init-context{% endif %}" itemprop="articleBody">

      {# Gallery support #}
      {% if post.photos and post.photos.length %}
        <div class="post-gallery" itemscope itemtype="http://schema.org/ImageGallery">
          {% set COLUMN_NUMBER = 3 %}
          {% for photo in post.photos %}
            {% if loop.index0 % COLUMN_NUMBER === 0 %}<div class="post-gallery-row">{% endif %}
              <a class="post-gallery-img fancybox"
                 href="{{ url_for(photo) }}" rel="gallery_{{ post._id }}"
                 itemscope itemtype="http://schema.org/ImageObject" itemprop="url">
                <img src="{{ url_for(photo) }}" itemprop="contentUrl"/>
              </a>
            {% if loop.index0 % COLUMN_NUMBER === 2 %}</div>{% endif %}
          {% endfor %}

          {# Append end tag for `post-gallery-row` when (photos size mod COLUMN_NUMBER) is less than COLUMN_NUMBER #}
          {% if post.photos.length % COLUMN_NUMBER > 0 %}</div>{% endif %}
        </div>
      {% endif %}

      {% if is_index %}
        {% if post.description and theme.excerpt_description %}
          {{ post.description }}
          <!--noindex-->
          <div class="post-button text-center">
            <a class="btn" href="{{ url_for(post.path) }}">
              {{ __('post.read_more') }} &raquo;
            </a>
          </div>
          <!--/noindex-->
        {% elif post.excerpt  %}
          {{ post.excerpt }}
          <!--noindex-->
          <div class="post-button text-center">
            <a class="btn" href="{{ url_for(post.path) }}{% if theme.scroll_to_more %}#{{ __('post.more') }}{% endif %}" rel="contents">
              {{ __('post.read_more') }} &raquo;
            </a>
          </div>
          <!--/noindex-->
        {% elif theme.auto_excerpt.enable %}
          {% set content = post.content | striptags %}
          {{ content.substring(0, theme.auto_excerpt.length) }}
          {% if content.length > theme.auto_excerpt.length %}...{% endif %}
          <!--noindex-->
          <div class="post-button text-center">
            <a class="btn" href="{{ url_for(post.path) }}{% if theme.scroll_to_more %}#{{ __('post.more') }}{% endif %}" rel="contents">
              {{ __('post.read_more') }} &raquo;
            </a>
          </div>
          <!--/noindex-->
        {% else %}
          {% if post.type === 'picture' %}
            <a href="{{ url_for(post.path) }}">{{ post.content }}</a>
          {% else %}
            {{ post.content }}
          {% endif %}
        {% endif %}
      {% else %}
        {% include '../_custom/google_adsense.swig' %}
        {{ post.content }}
      {% endif  %}
    </div>
```
注意，这里不要搞错，误插入到*\themes\next\layout\post.swig *文件中了，这样不仅仅会导致每篇文章里存在广告，在首页的列表中，每篇文章同样也都会带上广告，导致首页一次刷出来N个广告，影响网站布局和设计。

## 注意事项
在成功接入AdSense广告之后，并不算结束，Google会根据几种方式和数据判断广告点击是否作弊，从而注销你的账号。所以不要心存侥幸心理，好好发原创文章，提高网站的质量才是王道。

1. 作弊广告点击者的IP地址与你Adsense账户登录IP地址相同
2. 作弊广告点击的CTR数据太高
3. 作弊广告点击者的IP地址来自同一个地理区域
4. 根据Cookies判断作弊Adsense广告点击
5. 作弊广告点击者页面停留时间太短
6. 直接访问者的广告点击率过高
7. 流量小但广告点击率高
8. 在网页上用文字提示请求鼓动点击广告

## 说在最后
现在通过adsense本身能赚到的收入本身并不高，尤其是博客类的网站更是如此，但是既然大家愿意花时间和精力去搭建个人博客，除了钱以外，肯定还有其他的目的，希望大家不要舍本逐末，忘记了自己搭建博客的初衷，毕竟广告收入这个事情，有当然好，没有也不用气馁，尽力就好～
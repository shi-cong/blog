# dota2官方视屏直播数据抓取分析

1. .m4s
2. octet-stream

## 知识
1 octet-stream知识

    直接用 application/octet-stream代替了，表示任意的二进制文件。 不会影响图片本身内容的。实际的文件mimetype是依据文件开头的几个字节来判定的。

2 .m4s知识

    如何在播放器上 根据MPEG-DASH给定的.mpd文件信息 播放 .m4s 文件？
    是这样的，对一个原视频进行切片，得到后缀为.m4s的每个片段，还有一个关于视频切片信息的.mpd文件，如何根据.mpd文件完整播放每个片段，达到直接播放原视频的效果？

## 抓包
* http://tx2play1.douyucdn.cn/video/3657961rd7mTebHs/UHD/5a213e0f.m4s
* 3657961rd7mTebHs.flv?wsAuth=e5ffe0699e15685e1aa26d033de14eec&token=web-douyu-0-3657961-725de91d2acd3753fcf670b4ffaeb2f2&logo=0&expire=0&did=AB3C556187DA1E0ED565DA3CADB31FE1&ver=2017092607
* http://hdl1a.douyucdn.cn/live

`http://hdl1a.douyucdn.cn/live/3657961rd7mTebHs.flv?wsAuth=e5ffe0699e15685e1aa26d033de14eec&token=web-douyu-0-3657961-725de91d2acd3753fcf670b4ffaeb2f2&logo=0&expire=0&did=AB3C556187DA1E0ED565DA3CADB31FE1&ver=2017092607`可以播放一点片段

这个请求要分析下
`http://www.douyu.com/swf_api/openShare/3657961?sign=be28ecfbafaddf3a33e5729a364363e1&_t=25202147`

还不大清楚，细节

## 查看该网站关键的js
```javascript
$(function(){
	var J_tabs = $('.tabs>li'),
		J_lang_item = $('.lang-item>li'),
		J_lang = $('.lang>span'),
		J_video_box = $('.video-box'),
		liveTemp = $('#j-liveTemp'),
		items = $('.tabs-item-box>li'),
		J_cat_btn = $('.cat-btn'),
		date = "2017-9-27";

	var VideoUrl = {
		huomao: ['https://www.huomao.com/outplayer/index/948916','https://www.huomao.com/outplayer/index/5103'],
		douyu: ['https://staticlive.douyucdn.cn/common/share/play.swf?room_id=3657961','https://staticlive.douyucdn.cn/common/share/play.swf?room_id=3688429'],
		// xiongmao: ['http://www.panda.tv/liveplay/309495'],
  //       yahu: ['http://liveshare.huya.com/474682403/huyacoop.swf'],
  //       zanqi: ['http://www.zhanqi.tv/live/embed?roomId=27839&fhost=http%3A%2F%2Fwww.wanmei.com%2F'] 
        // 173: ['http://www.173.com/share/69', 
        // 'http://www.173.com/share/68', 
        // 'http://www.173.com/share/67']
    }

	//舞台切换
	J_tabs.click(function(){
		var TPL = new Pwrd_Template();
		var that = $(this);

		var iframe = J_video_box.find('iframe');
		var temp;

		if(that.index() !== 3){
			var src = that.attr('data-url');
			var isSwf = src.indexOf('swf') > -1;
			if (iframe.length > 0 && !isSwf) {
                iframe.attr('src', src);
            } else {
                temp = TPL.template(liveTemp.html());
                J_video_box.html(temp({
                    data: src,
                    isSwf: isSwf
                }));
            }
			that.addClass('live-active').siblings().removeClass('live-active');
		}
	});

	//语言切换
	// J_lang_item.click(function(){
	// 	var that = $(this);

	// 	J_lang.text(that.text());
	// 	that.parent().hide();
	// 	J_lang.show();

	// });

	// $('.lang').hover(function(){
	// 	J_lang.hide();
	// 	$('.lang-item').show();
	// },function(){
	// 	J_lang.show();
	// 	$('.lang-item').hide();
	// });

	// 切换直播源
	items.click(function(){
		var that = $(this);
		var TPL = new Pwrd_Template();

		var iframe = J_video_box.find('iframe');
		var temp;
		
		var src = VideoUrl[that.attr('data-name')];


		if(src != undefined){
			var isSwf = src.indexOf('swf') > -1;
			if (iframe.length > 0 && !isSwf) {
	            iframe.attr('src', src[0]);
	        } else {
	            temp = TPL.template(liveTemp.html());
	            J_video_box.html(temp({
	                data: src[0],
	                isSwf: isSwf
	            }));
	        }
	        that.addClass('active').siblings().removeClass('active');
		}
		for(var i=0; i<J_tabs.length; i++){
			J_tabs.eq(i).attr('data-url',VideoUrl[that.attr('data-name')][i])
		}
		J_tabs.eq(0).trigger('click').addClass('live-active').siblings().removeClass('live-active')



	});

	// 观赛指南
	$('#guide-area-date').children().on('click',function(){
		var index=$(this).index();
		date = $(this).attr('data-date');
		$(this).addClass('active').siblings().removeClass('active');
		$('.guide-area').text($(this).data('title'));
		$('#guide-area-table').children().eq(index).show().siblings().hide();
	})

	//查看对阵图
	J_cat_btn.click(function(){
		if(window.location.href.indexOf('chinese') !=-1){
			window.location.href = "/pwrdmasters/2017/chinese/match/?date="+date+"#j-mapwarp";
		}else{
			window.location.href = "/pwrdmasters/2017/english/match/?date="+date+"#j-mapwarp";
		}
	});
})
```
吗的，这得我自己先写个flash的视屏播放的demo才能拿到这个视屏的详细数据。

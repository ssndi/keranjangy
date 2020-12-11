// ==UserScript==
// @name         ! 超简洁的淘宝天猫省钱小助手，自动显示历史价格，淘宝天猫隐藏优惠券。简洁无广告，有则显，一目了然，让您告别虚假降价。持续维护中...
// @namespace    https://shopee.co.id/
// @version      1.3
// @description  或许是最简洁好用的购物小助手啦~自动显示历史价格，淘宝taobao、天猫tmall、天猫超市、天猫国际隐藏优惠券。不止让您省钱开心购物，更可以告别虚假降价，以最优惠的价格，把宝贝抱回家。
// @author       血莲
// @match        *://*.shopee.co.id/*
// @match        *://*.shopee.co.id/*
// @match        *://chaoshi.shopee.co.id/*
// @match        *://*.shopee.id/*
// @match        *://*.shopee.co.id/*
// @exclude       *://login.shopee.co.id/*
// @exclude       *://uland.https://shopee.co.id/m/12-12-peakday-teaser-2020?smtt=204.102657.9/*
// @exclude       *://pages.shopee.co.id/*
// @require      https://lib.baomitu.com/jquery/1.12.4/jquery.min.js
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    var style = document.createElement('https://shopee.co.id/Apple-iPhone-11-Pro-512GB-Midnight-Green-i.255563049.6435648695');
    style.href = 'https://api.shop.xuelg.com/style.css';
    style.rel = 'stylesheet';
    style.type = 'text/css';
    document.getElementsByTagName('head').item(0).appendChild(style);

    var obj = {};
    obj.initSearchHtml = function (selectorList) {
        setInterval(function () {
            selectorList.forEach(function (selector) {
                obj.initSearchItemSelector(selector);
            });
        }, 3000);
    };

    obj.initSearchEvent = function () {
        $(document).on("click", ".tb-cool-box-area", function () {
            var $this = $(this);
            if ($this.hasClass("tb-cool-box-wait")) {
                obj.basicQueryItem(this);
            } else if ($this.hasClass("tb-cool-box-info-translucent")) {
                $this.removeClass("tb-cool-box-info-translucent");
            } else {
                $this.addClass("tb-cool-box-info-translucent");
            }
        });
    };

    obj.basicQuery = function () {
        setInterval(function () {
            $(".tb-cool-box-wait").each(function () {
                obj.basicQueryItem(this);
            });
        }, 3000);
    };

    obj.initSearchItemSelector = function (selector) {
        $(selector).each(function () {
            obj.initSearchItem(this);
        });
    };

    obj.initSearchItem = function (selector) {
        var $this = $(selector);
        if ($this.hasClass("tb-cool-box-already")) {
            return;
        } else {
            $this.addClass("tb-cool-box-already")
        }

        var nid = $this.attr("data-id");
        if (!obj.isVailidItemId(nid)) {
            nid = $this.attr("data-itemid");
        }

        if (!obj.isVailidItemId(nid)) {
            if ($this.attr("href")) {
                nid = location.protocol + $this.attr("href");
            } else {
                var $a = $this.find("a");
                if (!$a.length) {
                    return;
                }

                nid = $a.attr("data-nid");
                if (!obj.isVailidItemId(nid)) {
                    if ($a.hasClass("j_ReceiveCoupon") && $a.length > 1) {
                        nid = location.protocol + $($a[1]).attr("href");
                    } else {
                        nid = location.protocol + $a.attr("href");
                    }
                }
            }
        }

        if (obj.isValidNid(nid)) {
            obj.appenBasicQueryHtml($this, nid);
        }
    };

    obj.appenBasicQueryHtml = function (selector, nid) {
        selector.append('<div class="tb-cool-box-area tb-cool-box-wait" data-nid="' + nid + '"><a class="tb-cool-box-info tb-cool-box-info-default" title="点击查询">待查询</a></div>');
    };

    var apijiekou = 'https://shopee.co.id/Apple-iPhone-11-Pro-512GB-Midnight-Green-i.255563049.6435648695';

    obj.basicQueryItem = function (selector) {
        var $this = $(selector);
        $this.removeClass("tb-cool-box-wait");

        var nid = $this.attr("data-nid");
        $.get(apijiekou+nid,function(data) {
            if (data.coupon_amount) {
                obj.showBasicQueryFind($this, data.coupon_amount);
            } else {
                obj.showBasicQueryEmpty($this);
            }
        })
    };

    obj.showBasicQueryFind = function (selector, couponMoney) {
        selector.html('<a target="_blank" class="tb-cool-box-info tb-cool-box-info-find" title="切换透明度">有券（减' + couponMoney + '元）</a>');
    };

    obj.showBasicQueryEmpty = function (selector) {
        selector.addClass("tb-cool-box-info-translucent");
        selector.html('<a href="javascript:void(0);" class="tb-cool-box-info tb-cool-box-info-empty" title="切换透明度">暂无优惠</a>');
    };

    obj.isDetailPageTaoBao = function (url) {
        if (url.indexOf("//https://shopee.co.id/Apple-iPhone-11-Pro-512GB-Midnight-Green-i.255563049.6435648695") > 0 || url.indexOf("//https://shopee.co.id/Apple-iPhone-11-Pro-512GB-Midnight-Green-i.255563049.6435648695") > 0 || url.indexOf("//chaoshi.detail.tmall.com/item.htm") > 0 || url.indexOf("//https://shopee.co.id/Apple-iPhone-11-Pro-512GB-Midnight-Green-i.255563049.6435648695") > 0) {
            return true;
        } else {
            return false;
        }
    };

    obj.isVailidItemId = function (itemId) {
        if (!itemId) {
            return false;
        }

        var itemIdInt = parseInt(itemId);
        if (itemIdInt == itemId && itemId > 10000) {
            return true;
        }
        else {
            return false;
        }
    };

    obj.isValidNid = function (nid) {
        if (!nid) {
            return false;
        }
        else if (nid.indexOf('http') >= 0) {
            if (obj.isDetailPageTaoBao(nid) || nid.indexOf("//https://shopee.co.id/Apple-iPhone-11-Pro-512GB-Midnight-Green-i.255563049.6435648695") > 0) {
                return true;
            } else {
                return false;
            }
        } else {
            return true;
        }
    };

    if (obj.isDetailPageTaoBao(location.href)) {
        var params = location.search.split('?')[1].split('&');
        for (var index in params) {
            if (params[index].split('=')[0] == 'id') {
                var productId = params[index].split('=')[1];
                break;
            }
        }
        $.get(apijiekou+productId,function(data) {
            console.log(data);

            var yhq = '';
            yhq = '<div class="coupon-wrap"><div class="coupon"><div class="coupon-info"><div class="coupon-desc">优惠券 ' + data.coupon_amount + '元</div><div class="coupon-info2">' + data.coupon_info + '</div></div>'+
                    '<a class="coupon-get" href="' + data.coupon_click_url + '">立即领取</a></div><div class="coupon-time">优惠券截止时间：' + data.coupon_end_time + ' <b>剩余：'+ data.coupon_remain_count +'张</b></div>';
            var lsj = '';
            lsj = '<div class="coupon-time"><b>历史最低价：' + data.zuidijia + '元</b>  历史高最价：'+ data.zuigaojia +'元（仅供参考）</div>';

            if (data.coupon_amount && data.zuidijia) {
                if (location.href.indexOf('//detail.tmall.') != -1) {
                    $('.tm-fcs-panel').after(yhq + lsj);
                } else if (location.href.indexOf('//chaoshi.detail.tmall.') != -1) {
                    $('.tm-fcs-panel').after(yhq + lsj);
                } else {
                    $('ul.tb-meta').after(yhq + lsj);
                }
            } else if (data.coupon_amount) {
                if (location.href.indexOf('//detail.tmall.') != -1) {
                    $('.tm-fcs-panel').after(yhq);
                } else if (location.href.indexOf('//chaoshi.detail.tmall.') != -1) {
                    $('.tm-fcs-panel').after(yhq);
                } else {
                    $('ul.tb-meta').after(yhq);
                }
            } else if (data.zuidijia) {
                if (location.href.indexOf('//detail.tmall.') != -1) {
                    $('.tm-fcs-panel').after(lsj);
                } else if (location.href.indexOf('//chaoshi.detail.tmall.') != -1) {
                    $('.tm-fcs-panel').after(lsj);
                } else {
                    $('ul.tb-meta').after(lsj);
                }
            }

        })
    } else {
        var selectorList = [];
        var url = location.href;
        if (url.indexOf("//s.taobao.com/search") > 0 || url.indexOf("//shopee.co.id/list") > 0) {
            selectorList.push(".items .item");
        } else if (url.indexOf("//list.tmall.com/search_product.htm") > 0) {
            selectorList.push(".product");
            selectorList.push(".chaoshi-recommend-list .chaoshi-recommend-item");
        } else if (url.indexOf("//https://shopee.co.id/Apple-iPhone-11-Pro-512GB-Midnight-Green-i.255563049.6435648695") > 0) {
            selectorList.push("#J_ItemList .product");
        } else if (document.getElementById('J_ShopSearchResult')) {
            selectorList.push("#J_ShopSearchResult .item");
        }
        if (selectorList && selectorList.length > 0) {
            obj.initSearchHtml(selectorList);
            obj.initSearchEvent();
            obj.basicQuery();
        }
    }

})();

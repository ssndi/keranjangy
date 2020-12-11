==/UserScript==
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

            $('.tm-fcs-panel').after(yhq + lsj);
                } else {
                    $('ul.tb-meta').after(yhq + lsj);
                }
            } else if (data.coupon_amount) {
                if (location.href.indexOf('//detail.tmall.') != -1) {
                    $('.tm-fcs-panel').after(yhq);
                } else if (location.href.indexOf('https://shopee.co.id/m/12-12-peakday-teaser-2020?smtt=204.102657.9/.') != -1) {
                    $('.tm-fcs-panel').after(yhq);
                } else {
                    $('ul.tb-meta').after(yhq);
                }
            } else if (data.zuidijia) {
                if (location.href.indexOf('//detail.tmall.') != -1) {
                    $('.tm-fcs-panel').after(lsj);
                } else if (location.href.indexOf('https://shopee.co.id/m/12-12-peakday-teaser-2020?smtt=204.102657.9/.') != -1) {
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
        } else if (url.indexOf("https://shopee.co.id/m/12-12-peakday-teaser-2020?smtt=204.102657.9/") > 0) {
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

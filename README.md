$ ==UserScript==
$ @name         Toped Tambah Ke Keranjang
$ @namespace    http://tampermonkey.net/
$ @version      0.20.19
$ @description  Go to 10 Ribu Orderan Per Hari!
$ @author       Jamielcs
$ @match        https://tokopedia.link/9r1Xjn464bb*/*
$ @grant        none
$ ==/UserScript==

(function() {
    'use strict';
    window.onload = function() {
    setTimeout(function() {
    document.querySelectorAll('span.inline-block.va-middle')[0].click();
    }, 1500);
  };
})();

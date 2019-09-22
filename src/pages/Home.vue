<template>
  <div>
    <section class="page1" onclick="mantis.requestChat()">
      <div class="header">
        <div class="lefts">
          <div class="imgs"><img src="../../static/images/logo.png" alt=""></div>
          <div class="fonts">亲子关系课</div>
        </div>
      
      </div>
      <div class="centers">
        <div><span><img src="../../static/images/ren.png" alt=""></span> 剩余名额
          <countTo :startVal='startVal' :endVal='endVal' :duration='9000000'></countTo>
        </div>
      </div>
      <div class="rights">
        <div>元</div>
        <div>8.9</div>
        <div>限时</div>
      </div>
      <div class="banner"><img src="../../static/images/banner.jpg" alt="" width="100%"></div>
    </section>
    <section class="page2" onclick="mantis.requestChat()">
      <img src="../../static/images/wenti.jpg" alt="" width="100%">
    </section>
    <section class="page2" onclick="mantis.requestChat()">
      <img src="../../static/images/chengzhnag.jpg" alt="" width="100%">
    </section>
    <section class="page2" onclick="mantis.requestChat()">
      <img src="../../static/images/dedao.jpg" alt="" width="100%">
    </section>
    <section class="page2" onclick="mantis.requestChat()">
      <img src="../../static/images/laoshi.jpg" alt="" width="100%">
    </section>
    <section class="page2" onclick="mantis.requestChat()">
      <img src="../../static/images/pingjia.jpg" alt="" width="100%">
    </section>
    <section class="page2" onclick="mantis.requestChat()">
      <img src="../../static/images/fangshi.jpg" alt="" width="100%">
    </section>
    <div class="footer">
      <div>¥8.9元 <span style="font-size: 2rem;color: black;font-weight: lighter;text-decoration:line-through">  &nbsp; ¥699</span>
      </div>
      <div @click="startWxPay">立即报名</div>
    </div>
  </div>
</template>

<script>
  import {createWXPay2, getUserIsPay, isPay} from "../api/home";
  import countTo from 'vue-count-to';
  export default {
    name: "Home",
    components: { countTo },
    data() {
      return {
        startVal: 300,
        endVal: 0,
        wxCode: '',
        isPay: false,
        isPayMessage: '￥8.9 立即报名'
      }
    },
    watch: {
      wxCode: {
        handler(val) {
          this.getWxCode(val)
        }
      }
    },
    created() {
      
      this.wxCode = window.__globalCode;
      let appid = window.__globalAppId;
      let local = window.location.href;
      
      console.log('nan created1', this.wxCode)
      console.log('nan created3', appid)
      console.log('nan created4', local)
      
      
      // this.intentRegister()
      
      
    },
    methods: {
      intentRegister() {
        let u = navigator.userAgent, app = navigator.appVersion;
        let isAndroid = u.indexOf('Android') > -1 || u.indexOf('Linux') > -1; //g
        let appid = 'wx0b2a2de1d89dbc61'
        
        if (isAndroid) {
          console.log('nan intentRegister--------->', this.wxCode)
          if (this.wxCode === '') {
            let url = 'http://wechatclass.51nicelearn.com/#/nicehome'
            window.location.href = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid='
              + appid + '&redirect_uri=' + encodeURIComponent(url) + '&response_type=code&scope=snsapi_base&state=STATE#wechat_redirect';
          } else {
            let data = {}
            data.code = this.wxCode
            
            getUserIsPay(data).then(res => {
              console.log('nan 获取openId--->', res)
              if (res.status) {
                isPay(res.result.openid, 2).then(res => {
                  console.log('nan res 是否购买', res)
                  this.isPay = res.status
                  this.isPayMessage = res.status ? '您已购课，去学习' : '￥8.9 立即报名';
                })
              }
            })
          }
        }
      },
      startWxPay() {
        console.log('nan startWxPay', 'ceshi')
        let vm = this
        // console.log('nan startWxPay0', docCookies)
        
        this.wxCode = window.__globalCode;
        // let openId = window.__globalOpenId;
        let appid = window.__globalAppId;
        let local = window.location.href;
        
        console.log('nan startWxPay code 1', this.wxCode)
        // console.log('nan startWxPay openId 2', openId)
        console.log('nan startWxPay appid 3', appid)
        console.log('nan startWxPay local 4', local)
        
        // 如果没有code 或 openId，则去请求
        console.log('nan startWxPay', !this.wxCode)
        if (!this.wxCode) {
          // https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx0b2a2de1d89dbc61&redirect_uri=
          // http://cps.dianping.com/weiXinRedirect&response_type=code&scope=snsapi_base
          let url = 'http://wechatclass.51nicelearn.com/#/nicehome'
          window.location.href = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid='
            + appid + '&redirect_uri=' + encodeURIComponent(url) + '&response_type=code&scope=snsapi_base&state=STATE#wechat_redirect';
          
          // console.log('nan startWxPay-------->', openId)
        } else {
          // 调用接口
          let params = {
            'subject': '亲子关系课',
            'course_id': 2,
            'code': this.wxCode
          }
          createWXPay2(params).then(res => {
            console.log('nan res------------>', res)
            console.log('nan res--parse---------->', res.result)
            if (res.status === true) {
              const pay_params = res.result
              if (typeof WeixinJSBridge == "undefined") {
                if (document.addEventListener) {
                  document.addEventListener('WeixinJSBridgeReady', vm.onBridgeReady, false);
                } else if (document.attachEvent) {
                  document.attachEvent('WeixinJSBridgeReady', vm.onBridgeReady);
                  document.attachEvent('onWeixinJSBridgeReady', vm.onBridgeReady);
                }
              } else {
                this.onBridgeReady(pay_params);
              }
            } else {
              alert('微信支付调起失败!');
            }
          })
        }
      },
      getWxCode(val) {
        let u = navigator.userAgent, app = navigator.appVersion;
        let isAndroid = u.indexOf('Android') > -1 || u.indexOf('Linux') > -1; //g
        if (isAndroid) {
          return
        }
        let vm = this
        console.log('nan getWxCode--=======================->', val)
        // 调用接口
        let params = {
          'subject': '亲子关系课',
          'course_id': 2,
          'code': this.wxCode
        }
        createWXPay2(params).then(res => {
          // console.log('nan res1------------>', res)
          // console.log('nan res--status-1--------->', res.status)
          // console.log('nan res--result-1--------->', res.result)
          if (res.status === true) {
            const pay_params = res.result
            if (typeof WeixinJSBridge == "undefined") {
              if (document.addEventListener) {
                document.addEventListener('WeixinJSBridgeReady', vm.onBridgeReady, false);
              } else if (document.attachEvent) {
                document.attachEvent('WeixinJSBridgeReady', vm.onBridgeReady);
                document.attachEvent('onWeixinJSBridgeReady', vm.onBridgeReady);
              }
            } else {
              this.onBridgeReady(pay_params);
            }
          } else {
            alert('微信支付调起失败!');
          }
        })
      },
      onBridgeReady(params) {
        // console.log('nan onBridgeReady', params)
        WeixinJSBridge.invoke(
          'getBrandWCPayRequest', {
            "appId": params.appId,  //公众号名称，由商户传入
            "timeStamp": params.timeStamp.toString(),  //时间戳，自1970年以来的秒数
            "nonceStr": params.nonceStr,  //随机串
            "package": params.package,
            "signType": params.signType,  //微信签名方式：
            "paySign": params.paySign  //微信签名
          },
          function (res) {
            if (res.err_msg === 'get_brand_wcpay_request:ok') {
              // console.log('nan ', '支付成功！');
              window.location.href = 'http://wechatclass.51nicelearn.com/#/coder'
            } else if (res.err_msg === 'get_brand_wcpay_request:cancel') {
              console.log('nan ', '取消了支付')
              alert("取消了支付")
            } else {
              alert('支付失败')
            }
          });
      },
    }
  }
  
  
  let docCookies = {
    getItem: function (sKey) {
      return decodeURIComponent(document.cookie.replace(new RegExp("(?:(?:^|.*;)\\s*"
        + encodeURIComponent(sKey).replace(/[-.+*]/g, "\\$&") + "\\s*\\=\\s*([^;]*).*$)|^.*$"), "$1")) || null;
    },
    setItem: function (sKey, sValue, vEnd, sPath, sDomain, bSecure) {
      if (!sKey || /^(?:expires|max\-age|path|domain|secure)$/i.test(sKey)) {
        return false;
      }
      var sExpires = "";
      if (vEnd) {
        switch (vEnd.constructor) {
          case Number:
            sExpires = vEnd === Infinity ? "; expires=Fri, 31 Dec 9999 23:59:59 GMT" : "; max-age=" + vEnd;
            break;
          case String:
            sExpires = "; expires=" + vEnd;
            break;
          case Date:
            sExpires = "; expires=" + vEnd.toUTCString();
            break;
        }
      }
      document.cookie = encodeURIComponent(sKey) + "=" + encodeURIComponent(sValue)
        + sExpires + (sDomain ? "; domain=" + sDomain : "") + (sPath ? "; path=" + sPath : "") + (bSecure ? "; secure" : "");
      return true;
    },
    removeItem: function (sKey, sPath, sDomain) {
      if (!sKey || !this.hasItem(sKey)) {
        return false;
      }
      document.cookie = encodeURIComponent(sKey) + "=; expires=Thu, 01 Jan 1970 00:00:00 GMT"
        + (sDomain ? "; domain=" + sDomain : "") + (sPath ? "; path=" + sPath : "");
      return true;
    },
    hasItem: function (sKey) {
      return (new RegExp("(?:^|;\\s*)" + encodeURIComponent(sKey).replace(/[-.+*]/g, "\\$&") + "\\s*\\=")).test(document.cookie);
    },
    keys: /* optional method: you can safely remove it! */ function () {
      var aKeys = document.cookie.replace(/((?:^|\s*;)[^\=]+)(?=;|$)|^\s*|\s*(?:\=[^;]*)?(?:\1|$)/g, "").split(/\s*(?:\=[^;]*)?;\s*/);
      for (var nIdx = 0; nIdx < aKeys.length; nIdx++) {
        aKeys[nIdx] = decodeURIComponent(aKeys[nIdx]);
      }
      return aKeys;
    }
  };
  
  let utils = {
    getUrlQuery: function (name) {
      var result = location.search.match(new RegExp("[\?\&]" + name + "=([^\&]+)", "i"));
      if (result == null || result.length < 1) {
        return "";
      }
      return result[1];
    }
  };
  window.__globalCode = utils.getUrlQuery('code')
  window.__globalOpenId = docCookies.getItem('openId')
  window.__globalAppId = 'wx0b2a2de1d89dbc61'
</script>
<style scoped>
  @import "../../static/css/wxse.css";
</style>



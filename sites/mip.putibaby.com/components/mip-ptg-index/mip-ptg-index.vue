<template>
  <div class="wrapper">
    <div class="top_level_1">
      <mip-carousel
        autoplay
        defer="3000"
        width="400"
        height="277"
        layout="responsive"
        indicator-id="mip-carousel-example">
        <mip-img src="/i/b1.png"/>
        <mip-img src="/i/b2.png"/>
        <mip-img src="/i/b3.png"/>
      </mip-carousel>
      <div class="mip-carousel-indicator-wrapper">
        <div
          id="mip-carousel-example"
          class="mip-carousel-indicatorDot">
          <div class="mip-carousel-activeitem mip-carousel-indecator-item"/>
          <div class="mip-carousel-indecator-item"/>
          <div class="mip-carousel-indecator-item"/>
          <div class="mip-carousel-indecator-item"/>
        </div>
      </div>
    </div>
    <div>
      <a @click="handleCoupon">
        <mip-img src="/i/yhq_jt.png"/>
      </a>
    </div>
    <div>
      <div class="find">
        <a @click="handleSelectMaster">
          <mip-img src="/i/find_ys.png"/>
        </a>
      </div>
      <div class="help">
        <a @click="handleUpdateYcq">
          <mip-img src="/i/help_me.png"/>
        </a>
      </div>
    </div>

    <mip-fixed
      type="bottom"
      class="bottom">
      <a
        class="bottom_left"
        href="tel:4006188835">
        <mip-img
          src="/i/index_phone.png"
          width="132px"
          height="22px"
          class="bottom_left_img"/>
      </a>
      <a @click="handleOrderList">
        <mip-img
          src="/i/ind_person.png"
          width="38px"
          height="25px"
          class="bottom_right_img"/>
      </a>
    </mip-fixed>

  </div>
</template>

<style scoped>
  .wrapper {
    margin: 0 auto;
    text-align: center;
  }

  /* <!-- index.css --> */

  p {
    margin: 0px;
    padding: 0px;
    font-family: '黑体';
  }

  /* <!-- banner.css --> */

  .top_level_1 {
    width: 100%;
    position: relative;
  }

  mip-form form {
    position: relative;
    left: 0px;
    width: 90vw;
  }

  mip-form input[type='text'] {
    padding-right: 30px;
    width: 80%;
    margin: auto;
    position: relative;
    top: 20px;
    background-color: rgba(0, 0, 0, 0);
  }

  mip-form input[type='submit'] {
    border: 1px solid #fe7834;
    border-radius: 5px;
    color: #333;
    background-color: #fe7834;
    width: 98%;
    margin: auto;
    position: relative;
    top: 40px;
  }

  mip-carousel {
    width: 100%;
    height: 73.8%;
  }

  .mip-carousel-indicator-wrapper {
    text-align: center;
    background-color: rgba(0, 0, 0, .0);
    position: absolute;
    top: 130px;
    left: 190px;
  }

  .mip-layout-container {
    /* display: block; */
    position: relative;
    display: inline-block;
    width: 100%;
    margin: auto;
  }

  .mip-login-container {
    display: inline-block;
  }

  .test-login-div {
    margin-bottom: 130px;
  }

  mip-form div {
    display: none;
    color: #ec1f5c;
    font-size: 12px;
    text-align: left;
    padding: 0 10% 0 3%;
    position: relative;
    left: 30px;
    top: 28px;
  }

  .find {
    width: 44%;
    margin-left: 4%;
    margin-right: 2%;
    height: 150px;
    text-align: center;
    float: left;
  }

  .help {
    width: 44%;
    margin-left: 2%;
    margin-right: 4%;
    height: 150px;
    text-align: center;
    float: left;
  }

  .bottom {
    bottom: 0;
    height: 45px;
    line-height: 45px;
    background-color: #fff;
    width: 100%;
    z-index: 9999;
  }

  .bottom_left {
    width: 80%;
    height: 45px;
    text-align: center;
    display: inline-block;
    color: black;
    padding-left: 0;
  }

  .bottom_left_img {
    vertical-align: -6px;
  }

  .bottom_right_img {
    vertical-align: -6px;
  }
</style>

<script>
var API = {}

function checkStatus (response) {
  if (response.status >= 200 && response.status < 300) {
    return response
  } else {
    var error = new Error(response.statusText)
    error.response = response
    throw error
  }
}

function parseJSON (response) {
  return response.json()
}

API.wrapRet_ = function (api, opts, fn) {
  console.log('posting to ' + api)
  opts.mip_sid = API.sessionId || ''
  fetch(api, {
    method: 'POST',
    credentials: 'same-origin',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(opts)
  })
    .then(checkStatus)
    .then(parseJSON)
    .then(ret => {
      if (ret.success) {
        return fn(true, ret.data)
      } else {
        fn(false, ret.error)
      }
    })
    .catch(e => {
      console.error(e.message)
      fn(false, e.message)
    })
}

export default {

  props: {
    dataJsonstr: {
      type: String,
      default: null
    }
  },
  data () {
    console.log(this)
    // var pdata = JSON.parse(this.dataJsonstr);
    return {
      // data: pdata.data,
      isLogin: false,
      isUnion: false
    }
  },
  computed: {

  },
  mounted () {
    console.log('This is index component !')
    var self = this
    this.$element.customElement.addEventAction('logindone', event => {
      // 这里可以输出登录之后的数据

      // 获取用户信息
      console.log(event)
      API.sessionId = event.sessionId

      self.$set(self, 'isLogin', true)
      self.$set(self, 'isUnion', event.userInfo.isUnion)
      var origin = API.next_cmd || event.origin || localStorage.getItem('origin')

      if (event.userInfo.isUnion && origin === 'order_list') {
        console.log('logindone to order_list')
        console.log(window.MIP)
        window.MIP.viewer.open('https://mip.putibaby.com/order_list', {})
        API.next_cmd = ''
        sessionStorage.next_cmd = ''
        localStorage.clear()
      } else if (event.userInfo.isUnion && origin === 'coupon') {
        console.log('logindone to coupon')
        window.MIP.viewer.open('https://mip.putibaby.com/coupon', {})
        API.next_cmd = ''
        sessionStorage.next_cmd = ''
        localStorage.clear()
      } else if (event.userInfo.isUnion && origin === 'update_ycq') {
        console.log('logindone to update_ycq')
        window.MIP.viewer.open('https://mip.putibaby.com/update_ycq', {})
        API.next_cmd = ''
        sessionStorage.next_cmd = ''
        localStorage.clear()
      } else if (!event.userInfo.isUnion && origin) {
        console.log('logindone to submit_ph')
        var to = '/' + origin
        API.next_cmd = ''
        sessionStorage.next_cmd = ''
        localStorage.clear()
        window.MIP.viewer.open('https://mip.putibaby.com/submit_ph?to=' + encodeURIComponent(to), {})
      }
    })
  },
  methods: {
    init () {
      console.log('should loading')
    },
    created () {

    },

    checkLogin_ (cmd) {
      if (!this.isLogin) {
        API.next_cmd = cmd
        sessionStorage.next_cmd = cmd
        localStorage.setItem('origin', cmd)
        console.log(cmd)
        if (cmd === 'coupon') {
          this.$emit('login1')
        } else if (cmd === 'order_list') {
          this.$emit('login2')
        } else if (cmd === 'update_ycq') {
          this.$emit('login3')
        }
        return false
      }
      if (!this.isUnion) {
        // API.next_cmd = cmd;
        // sessionStorage.next_cmd = cmd;
        // localStorage.setItem('origin', cmd);
        var to = '/' + cmd
        window.MIP.viewer.open('https://mip.putibaby.com/submit_ph?to=' + encodeURIComponent(to), {})

        return false
      }

      return true
    },

    reload_ () {
      console.log('reloading')
    },
    handleCoupon () {
      console.log('handleCoupon')
      if (!this.checkLogin_('coupon')) { return }
      window.MIP.viewer.open('https://mip.putibaby.com/coupon ', {})
    },
    handleSelectMaster () {
      console.log('handleSelectMaster')
      // if (!this.checkLogin_('select_master'))
      //   return;
      window.MIP.viewer.open('https://mip.putibaby.com/select_master ', {})
    },
    handleUpdateYcq () {
      console.log('handleUpdateYcq')
      if (!this.checkLogin_('update_ycq')) { return }
      window.MIP.viewer.open('https://mip.putibaby.com/update_ycq ', {})
    },
    handleOrderList () {
      console.log('handleOrderList')
      if (!this.checkLogin_('order_list')) { return }
      window.MIP.viewer.open('https://mip.putibaby.com/order_list ', {})
    },
    load_data () {
      console.log('should set data')
    }
  }

}
</script>

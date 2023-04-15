<template>
  <div class="home">
    <div class="addAddress" v-show="step == 1">
      <h5>ثبت آدرس</h5>
      <div class="form">
        <div class="text">لطفا مشخصات و آدرس خود را وارد کنید</div>
        <div class="inputs">
          <div class="input">
            <div class="errorText" v-if="firstNameError">نام باید دارای 3 کاراکتر باشد</div>
            <img
              src="@/assets/icons/clear.svg"
              v-if="firstName != ''"
              @click="firstName = ''"
              class="clearIcon"
            />
            <label for="firstName">نام</label>
            <input
              id="firstName"
              :class="{ error: firstNameError }"
              v-model="firstName"
              @keyup="firstNameCheck"
              placeholder="مثال: محمد رضا"
              type="text"
            />
          </div>
          <div class="input">
            <div class="errorText" v-if="lastNameError">نام خانوادگی باید دارای 3 کاراکتر باشد</div>
            <img
              src="@/assets/icons/clear.svg"
              v-if="lastName != ''"
              @click="lastName = ''"
              class="clearIcon"
            />
            <label for="lastName">نام خانوادگی</label>
            <input
              id="lastName"
              :class="{ error: lastNameError }"
              v-model="lastName"
              @keyup="lastNameCheck"
              placeholder="مثال: رضایی"
              type="text"
            />
          </div>
          <div class="input">
            <div class="errorText" v-if="phoneNumberError">شماره وارد شده صحیح نمی باشد</div>
            <img
              src="@/assets/icons/clear.svg"
              v-if="phoneNumber != ''"
              @click="phoneNumber = ''"
              class="clearIcon"
            />
            <label for="phoneNumber">شماره تلفن همراه</label>
            <input
              id="phoneNumber"
              :class="{ error: phoneNumberError }"
              v-model="phoneNumber"
              @keyup="phoneNumberCheck"
              placeholder="مثال: 09121234567"
              type="text"
            />
          </div>
          <div class="input">
            <img
              src="@/assets/icons/clear.svg"
              v-if="phone != ''"
              @click="phone = ''"
              class="clearIcon"
            />
            <div class="labels">
              <label for="phone">شماره تلفن ثابت <span>(اختیاری)</span></label>
              <label for="phone" class="secondLabel">*با پیش شماره</label>
            </div>
            <input
              id="phone"
              :class="{ error: phoneError }"
              v-model="phone"
              @keyup="phoneCheck"
              placeholder="مثال: 0211234567"
              type="text"
            />
          </div>
          <div class="addressInput">
            <div class="errorText" v-if="addressError">آدرس باید حداقل 10 کاراکتر باشد</div>
            <img
              src="@/assets/icons/clear.svg"
              v-if="address != ''"
              @click="address = ''"
              class="clearIcon"
            />
            <label for="address">آدرس</label>
            <input
              id="address"
              :class="{ error: addressError }"
              v-model="address"
              @keyup="addressCheck"
              type="text"
            />
          </div>
          <div class="gender">
            <div class="title">جنسیت</div>
            <input type="radio" value="female" v-model="gender" />
            <label for="female">خانم</label>
            <input type="radio" value="male" v-model="gender" />
            <label for="male">آقا</label>
          </div>
        </div>
      </div>
    </div>
    <div class="selectLocation" v-show="step == 2">
      <div class="title">
        <img
          src="@/assets/icons/rightArrow.svg"
          @click="step = 1"
          alt="rightArrow"
          class="rightArrow"
        />
        <span class="desktop">انتخاب آدرس</span>
        <span class="mobile">انتخاب موقعیت</span>
      </div>
      <div class="mapDiv">
        <div class="mapTitle">موقعیت مورد نظر خود را روی نقشه مشخص کنید</div>
        <!-- before i used google map for get lat and lng buy now its filter and i wanted to use
        Neshan but it dont have select on map i sended ticket for them but didn't answer yet -->
        <input type="hidden" id="center_lat" />
        <input type="hidden" id="center_lng" />
        <div id="map"></div>
      </div>
    </div>
    <div class="confirmation" v-show="step == 3">
      <img src="@/assets/icons/success.svg" alt="successIcon" class="successIcon" />
      <div class="message">اطلاعات شما با موفقیت ثبت شد</div>
      <router-link to="/addresses"><button>مشاهده اطلاعات</button></router-link>
    </div>
    <!-- its an error if the server errored -->
    <div class="reqError" v-if="reqError">
      <span> مشکلی به وجود آمده است لطفا دوباره تلاش کنید </span>
    </div>

    <div class="nextStep" v-show="step != 3">
      <button @click="nextStep">
        <div v-if="!buttonAnimation">ثبت و ادامه</div>
        <!-- for waiting animation after send request to server -->
        <div v-if="buttonAnimation" class="balls">
          <span class="ball"></span>
          <span class="ball2"></span>
          <span class="ball"></span>
        </div>
      </button>
    </div>
  </div>
</template>

<script >
export default {
  data() {
    return {
      // step for step of add address
      step: 1,
      // list of informations
      firstName: '',
      lastName: '',
      phoneNumber: '',
      phone: '',
      address: '',
      gender: 'male',
      // validate form
      firstNameError: false,
      lastNameError: false,
      phoneNumberError: false,
      phoneError: false,
      addressError: false,
      // waiting animation after send request
      buttonAnimation: false,
      // error if server errored
      reqError: false,
      // its for check map just load 1 time
      mapLoad: true,

      centerLat: '',
      centerLng: ''
    }
  },
  mounted() {},
  methods: {
    // because you tell me dont use libraries i coded check inputs
    firstNameCheck() {
      if (this.firstName == '' || this.firstName.split('').length < 3) {
        this.firstNameError = true
      } else {
        this.firstNameError = false
      }
    },
    lastNameCheck() {
      if (this.lastName == '' || this.lastName.split('').length < 3) {
        this.lastNameError = true
      } else {
        this.lastNameError = false
      }
    },
    phoneNumberCheck() {
      if (
        // check number that was more than 11 number and have phone format
        this.phoneNumber == '' ||
        this.phoneNumber.split('').length != 11 ||
        this.phoneNumber.split('')[0] != 0 ||
        this.phoneNumber.split('')[1] != 9
      ) {
        this.phoneNumberError = true
      } else {
        // check number that dont have letters
        for (var i = 0; i < this.phoneNumber.split('').length; i++) {
          var num = parseInt(this.phoneNumber.split('')[i])
          if (
            num == 0 ||
            num == 1 ||
            num == 2 ||
            num == 3 ||
            num == 4 ||
            num == 5 ||
            num == 6 ||
            num == 7 ||
            num == 8 ||
            num == 9
          ) {
            this.phoneNumberError = false
          } else {
            this.phoneNumberError = true
            break
          }
        }
      }
    },
    // in file that you send me, tell me phone should be more than 11 num but in figma it has not error
    phoneCheck() {
      if (
        // check number that was more than 11 number and have phone format
        this.phone.split('').length != 11 ||
        this.phone.split('')[0] != 0
      ) {
        this.phoneError = true
      } else {
        // check number that dont have letters
        for (var i = 0; i < this.phone.split('').length; i++) {
          var num = parseInt(this.phone.split('')[i])
          if (
            num == 0 ||
            num == 1 ||
            num == 2 ||
            num == 3 ||
            num == 4 ||
            num == 5 ||
            num == 6 ||
            num == 7 ||
            num == 8 ||
            num == 9
          ) {
            this.phoneError = false
          } else {
            this.phoneError = true
            break
          }
        }
      }
    },
    addressCheck() {
      if (this.address == '' || this.address.split('').length < 10) {
        this.addressError = true
      } else {
        this.addressError = false
      }
    },
    // go to next step
    nextStep() {
      // check which step we are to send req or go next step
      if (this.step == 1) {
        // check all required fields entered and have our pattern
        if (this.firstName == '') {
          this.firstNameError = true
        }
        if (this.lastName == '') {
          this.lastNameError = true
        }
        if (this.phoneNumber == '') {
          this.phoneNumberError = true
        }
        if (this.address == '') {
          this.addressError = true
        }
        if (
          this.firstNameError == false &&
          this.lastNameError == false &&
          this.phoneNumberError == false &&
          this.phoneError == false &&
          this.addressError == false
        ) {
          this.step = 2
        }
      } else if (this.step == 2) {
        // its for starting button animation after click continue button
        this.buttonAnimation = true
        this.sendReq()
      }
    },
    // send request to server
    async sendReq() {
      try {
        let res = await this.axios.post(
          'https://stage.achareh.ir/api/karfarmas/address',
          {
            first_name: this.firstName,
            last_name: this.lastName,
            coordinate_mobile: this.phoneNumber,
            coordinate_phone_number: this.phone,
            address: this.address,
            region: 1,
            // before i used google map for get lat and lng buy now its filter and i wanted to use Neshan but it dont have select on map i sended ticket for them but didn't answer yet
            lat: '35.7717503',
            lng: '35.7717503',
            gender: this.gender
          },
          {
            headers: {
              Authorization: 'Basic MDk4MjIyMjIyMjI6U2FuYTEyMzQ1Njc4',
              'Content-Type': 'application/json'
            }
          }
        )
        this.step = 3
        console.log(res)
      } catch (error) {
        this.reqError = true
        setTimeout(() => {
          this.reqError = false
          this.buttonAnimation = false
          this.step = 1
        }, 2000)
        console.log(error)
      }
    }
  },
  watch: {
    // watch step to reload map
    step() {
      if (this.step == 2 && this.mapLoad == true) {
        setTimeout(() => {
          var centerLat = document.getElementById('center_lat')
          var centerLng = document.getElementById('center_lng')

          //init the map
          var myMap = new L.Map('map', {
            key: 'web.467ee09dbf474231b9e1f6391636cd0c',
            maptype: 'dreamy',
            poi: true,
            traffic: false,
            center: [35.699739, 51.338097],
            zoom: 14
          })
          //adding the marker to map
          var marker = L.marker([35.699739, 51.338097]).addTo(myMap)
          centerLat.value = '35.699739'
          centerLng.value = '51.338097'
          //on map binding
          myMap.on('click', addMarkerOnMap)

          //on map click function
          function addMarkerOnMap(e) {
            marker.setLatLng(e.latlng)
            centerLat.value = e.latlng.lat
            centerLng.value = e.latlng.lng
            reverse()
          }

          centerLng.addEventListener('keyup', function (event) {
            if (event.keyCode == 13) {
              marker.setLatLng([centerLat.value, centerLng.value])
              reverse()
            }
          })

          //sending request to Reverse API
          function reverse() {
            marker.setLatLng([centerLat.value, centerLng.value])
            var log = document.getElementById('log')
            //making url
            var url = `https://api.neshan.org/v2/reverse?lat=${centerLat.value}&lng=${centerLng.value}`
            //add your api key
            var params = {
              headers: {
                'Api-Key': 'service.6c9945dc5b254c40ae1f783420cf0d19'
              }
            }
            //sending get request
            this.axios
              .get(url, params)
              .then((data) => {
                myMap.flyTo([centerLat.value, centerLng.value], 16)
                marker.bindPopup(data.data.formatted_address).openPopup()
                document.getElementById('address').textContent = data.data.formatted_address
              })
              .catch((err) => {
                console.log('error = ' + err)
                log.textContent = 'Nothing found'
              })
          }
          this.centerLat = document.getElementById('center_lat')
          this.centerLng = document.getElementById('center_lng')
          this.mapLoad = false
        }, 10)
      }
    }
  }
}
</script>
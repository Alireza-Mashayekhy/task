<template>
  <div class="addressesPage">
    <div class="addresses">
      <div v-if="!waitingText && !errorText" class="title">آدرس ها و مشخصات</div>
      <div class="address" v-for="address in addressesList" :key="address.id">
        <div class="information">
          <div class="label">نام</div>
          <div class="info">{{ address.first_name }}</div>
        </div>
        <div class="information">
          <div class="label">نام خانوادگی</div>
          <div class="info">{{ address.last_name }}</div>
        </div>
        <div class="information">
          <div class="label">شماره تلفن همراه</div>
          <div class="info">{{ address.coordinate_mobile }}</div>
        </div>
        <div class="information">
          <div class="label">شماره تلفن ثابت</div>
          <div class="info" v-if="address.coordinate_phone_number != ''">
            {{ address.coordinate_phone_number }}
          </div>
          <div class="info" v-else>-</div>
        </div>
        <div class="information">
          <div class="label">جنسیت</div>
          <div class="info" v-if="address.gender != ''">{{ address.gender }}</div>
          <div class="info" v-else>-</div>
        </div>
        <div class="information addressInfo">
          <div class="label">آدرس</div>
          <div class="info">{{ address.address }}</div>
        </div>
      </div>
    </div>
    <!-- i wanted to use modals but you tell me dont use a lot libraries and i didnt and type text for handle requests -->
    <div class="waitingText" v-if="waitingText">لطفا منتظر بمانید...</div>
    <div class="errorText" v-if="errorText">مشکلی به وجود آمده است لطفا دوباره تلاش کنید</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // list for server data
      addressesList: [],
      // waiting message for get and load data
      waitingText: true,
      // error message for server errors
      errorText: false
    }
  },
  mounted() {
    this.getAddresses()
  },
  // get addresses from server after mounted
  methods: {
    async getAddresses() {
      try {
        let res = await this.axios.get('https://stage.achareh.ir/api/karfarmas/address', {
          headers: {
            Authorization: 'Basic MDk4MjIyMjIyMjI6U2FuYTEyMzQ1Njc4',
            'Content-Type': 'application/json'
          }
        })
        this.waitingText = false
        this.addressesList = res.data
      } catch (error) {
        this.waitingText = false
        this.errorText = true
        console.log(error)
      }
    }
  }
}
</script>

<style>
</style>
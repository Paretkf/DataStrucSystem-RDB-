<template>
  <div>
    <div class="columns is-centered">
      <div class="column is-4" style="margin: auto;text-align:center">
        <img src="../../assets/TA03.png" width="50%">
      </div>

      <div class="column is-8">
        <center>
          <p class="tableHead"> ตารางเวลา </p>
          <table class="table">
            <thead>
              <th>Time</th>
              <th>Name</th>
              <th>จอง</th>
            </thead>
            <tbody>
              <tr v-for="(schedule, key) in userDetails" :key="key">
                <th>{{schedule.time}}</th>
                <td v-if="schedule.name">{{schedule.name}}</td>
                <td v-else></td>
                <td v-if="schedule.ID && schedule.ID !== getUserLogin['.key']">
                  <b-field>
                    <b-radio-button
                      native-value="Disabled"
                      disabled>
                      Disabled
                    </b-radio-button>
                  </b-field>
                </td>
                <td v-else-if="(getUserLogin.schedule.time !== schedule.time && (getUserLogin.schedule.TA !== '' && getUserLogin.schedule.TA)) || (getUserLogin.schedule.TA !== '33' && getUserLogin.schedule.TA !== '')">
                  <b-field>
                    <b-radio-button
                      disabled
                      v-model="schedule.nativeValue"
                      :native-value="true"
                      type="is-success">
                      <b-icon icon="check"></b-icon>
                      <span>Yes</span>
                    </b-radio-button>

                    <b-radio-button
                      disabled
                      v-model="schedule.nativeValue"
                      :native-value="false"
                      type="is-danger">
                      <b-icon icon="close"></b-icon>
                      <span>No</span>
                    </b-radio-button>
                  </b-field>
                </td>
                <td v-else-if="!getUserLogin.schedule.TA || getUserLogin.schedule.TA === '33'">
                  <b-field>
                    <b-radio-button
                      v-model="schedule.nativeValue"
                      :native-value="true"
                      type="is-success"
                      @input="reservEventYes(schedule.time)">
                      <b-icon icon="check"></b-icon>
                      <span>Yes</span>
                    </b-radio-button>

                    <b-radio-button
                      v-model="schedule.nativeValue"
                      :native-value="false"
                      type="is-danger"
                      @input="reservEventNo(schedule.time)">
                      <b-icon icon="close"></b-icon>
                      <span>No</span>
                    </b-radio-button>
                  </b-field>
                </td>
              </tr>
            </tbody>
          </table>
        </center>
      </div>
    </div>
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
export default {
  name: 'TA3',
  data () {
    return {
      userDetails: []
    }
  },
  methods: {
    ...mapActions([
      'setReservTime',
      'setIsLoading',
      'solveSchedule'
    ]),
    async reservEventYes (value) {
      this.setIsLoading(true)
      await this.setReservTime({ time: value, TA: '33', status: true })
      await this.initData()
      this.setIsLoading(false)
    },
    async reservEventNo (value) {
      this.setIsLoading(true)
      await this.setReservTime({ time: value, TA: '33', status: false })
      await this.initData()
      this.setIsLoading(false)
    },
    async initData () {
      const taData = this.getTADetails[2]
      const userData = this.getUserLogin
      this.userDetails = taData.schedules.map((time) => {
        let schedules = {}
        if (userData.schedule.TA === '33' && userData.schedule.time === time.time && userData['.key'] === time.ID) {
          schedules = {
            ...time,
            nativeValue: true
          }
        } else {
          schedules = {
            ...time,
            nativeValue: false
          }
        }
        return schedules
      })
    }
  },
  computed: {
    ...mapGetters([
      'getUserLogin',
      'getTADetails'
    ])
  },
  async mounted () {
    await this.initData()
  },
  watch: {
    async getTADetails () {
      this.setIsLoading(true)
      await this.solveSchedule()
      await this.initData()
      this.setIsLoading(false)
    }
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css?family=Kanit');
.table {
  font-family: 'Kanit', sans-serif;
  text-align:center;
}
table td, table th {
  text-align:center;
  font-size: 0.7rem;
  vertical-align: middle;
}
.tableHead {
  font-family: 'Kanit', sans-serif;
  font-size: 100%;
  color:#fff;
  padding-bottom: 5%;
}
</style>

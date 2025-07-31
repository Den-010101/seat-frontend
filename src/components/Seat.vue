<template>
  <div>
    <h2>座位列表</h2>
    <ul>
      <li v-for="seat in seats" :key="seat.id">
        座位編號：{{ seat.SEAT_NO }}，樓層：{{ seat.FLOOR_NO }}，員工：{{ seat.EMP_ID || '無人' }}
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'SeatList',
  data() {
    return {
      seats: [],
    };
  },
  created() {
    axios.get('http://localhost:8080/api/seats')
      .then(response => {
        this.seats = response.data;
      })
      .catch(error => {
        console.error('取得座位失敗：', error);
      });
  }
};
</script>

<style scoped>
h2 {
  font-size: 20px;
  margin-bottom: 10px;
}
</style>
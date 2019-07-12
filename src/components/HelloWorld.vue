<template>
  <div>
    <h1>Бронирование билетов</h1>
    <p>Текущая дата {{nowDay}}</p>
    <p>
      Выберите дату 
      <select @click="disabled()" v-model="selected">
        <option
          v-for="a in options" 
          :key="a.id"
          >
            {{a}}
          </option>
      </select>
    </p>
    <p>
      Выберите сеанс
      <select @click="disabled()" v-model="selHour">
        <option v-for="a in hours" :key="a.id">{{a}}</option>
      </select>
    </p>
    <p v-if="!disable">Вы выбрали: {{place}}</p>
    <div class="cinema">
      <div
        v-for="(item, i) in ticket"
        :key="i"
        :class="{'active': item.showFace, 'reserve': item.reserved, 'disable': disable}"
        @click="item.showFace = !item.showFace, res = item, places()"
      >
        <p> </p>
      </div>
    </div>
    <button @click="reserved()">Забронировать</button>
  </div>
</template>

<script>
import { parse } from 'path';
  export default {
    // props: {
    //   ticket: {
    //     type: Array
    //   },
    // },
    data() {
      return {
        nowDay: '',
        now: Date.now(),
        hour: 0,
        day: 0,
        month: 0,
        year: 0,
        hours: ['10:00', '12:00', '14:00', '16:00', '18:00', '20:00'],
        res: null,
        place: '',
        disable: false,
        selected: '2019/07/14',
        selHour: '10:00',
        clicked: false,
        plcs: [], 
        count: 0,
        ticket: [],
        plcLenght: 0
      }
    },
    mounted () {
      this.updateNow()
      this.populateBoard()
      setInterval(this.updateNow.bind(this) , 1000)
      if (localStorage.getItem('plcs')) {
        try {
          this.plcs = JSON.parse(localStorage.getItem('plcs'));
          this.plcLenght =  this.plcs.length
        } catch(e) {
          localStorage.removeItem('plcs');
        }
      }
    },
    methods: {
      updateNow() {
        let d=new Date();
        let date = Math.round(d / 1000);
        this.hour = (Math.floor(date / 3600)) % 24 + 6
        this.day = d.getDate();
        this.month = d.getMonth() + 1;
        this.year =d.getFullYear();
        this.nowDay = this.year + '/' + this.month + '/' + this.day
      },
      populateBoard(){
        for(let i=1; i<=60; i++){
          this.ticket.push({id: i, showFace: false, reserved: false})
        }
      },
      reserved(item){
        this.ticket[this.res.id-1].reserved = true
        this.plcs.push({date: this.selected, hour: this.selHour, tckt: this.res.id-1})
        this.form()
        this.count++
      },
      places(){
        if(this.res !== null){
          this.place = Math.round(this.res.id/15) + ' ряд ' + Math.round(this.res.id%15) + ' место '
        }
      },
      form(){
        if(this.plcs !== []){
          for(let i=0; i<this.plcLenght; i++){
            let tckt = this.plcs[i].tckt
            if(this.selected === this.plcs[i].date && this.selHour === this.plcs[i].hour)
              this.ticket[tckt].reserved = true
          }
        this.saveTickets()
        // localStorage.clear();
        }
      },
      disabled(){
        let select = ''
        let selHour = this.selHour.slice(0, 2) - 0
        if(this.selected !== undefined)
        {
          select = this.selected.slice(-2) - 0
        }
        if(select <= this.day && selHour < this.hour){
          this.disable = true
        }
        else{
          this.disable = false
        }
        
        this.saveTickets()
        for(let i=0; i<60; i++){
          this.ticket[i].reserved = false
          this.ticket[i].showFace = false
        }
        this.form()
      },
      saveTickets(){
        const parsed = JSON.stringify(this.plcs);
        localStorage.setItem('plcs', parsed);
      }
      
    },
    computed: {
      options() {
        function checkTime(i)
        {
          if (i<10) 
          {
            i="0" + i;
          }
          return i;
        }
        let day = this.day - 7;
        let opt = [];
        for(var i=0; i<15; i++){
          if(this.year!==0)
            opt.push(this.year + '/' + checkTime(this.month) + '/' + checkTime(day + i))

        }
        return opt
      },
    },   
  }
</script>

<style>
  .cinema{
    display: flex;
    margin: 0 auto;
    width: 600px;
    flex-wrap: wrap;
  }
  .cinema > div{
    width: 27px;
    margin: 5px;
    background: rgb(177, 238, 56);
    height: 25px;
    border: 1px solid #000;
    cursor: pointer;
  }
  .cinema > div:hover{
    background: rgb(56, 120, 238);
  }
  .cinema .active{
    background: rgb(27, 161, 27);
  }
  .cinema .reserve, .cinema .reserve:hover{
    background: red;
  }
  .cinema .disable, .cinema .disable:hover{
    background: gray;
  }
</style>
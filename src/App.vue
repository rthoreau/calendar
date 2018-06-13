// eslint-disable-next-line
<template>
  <div id="app">
    <full-calendar :events="events" ref="calendar" :config="config"
    @event-selected="editEvent"
    @event-drop="eventDrop"
    @event-resize="eventResize"
    @event-receive="eventReceive"
    @event-created="showEventHandler"
    @event-render="eventRender"
    @day-click="dayClick"
    ></full-calendar>
    <button class="add-event-btn" v-if="!eventHandler" @click="showEventHandler()">+</button>
    <div class="add-event" v-if="eventHandler">
      <input type="text" v-model="title" ref="title" v-on:keyup.enter="saveEvent(id)">
      <input type="datetime" v-model="start">
      <input type="datetime" v-model="end">
      <input type="hidden" v-model="id">
      <button @click="saveEvent(id)">{{id ? 'Save Event' : 'Add Event'}}</button>
      <button class="add-event-btn" @click="showEventHandler(false, true)">X</button>
    </div>
  </div>
</template>

<script>
const TokenGenerator = require('uuid-token-generator');
const tokgen = new TokenGenerator();
import { FullCalendar } from 'vue-full-calendar'
import 'fullcalendar/dist/locale/fr'

export default {
  name: 'app',
  data () {
    return {
      eventHandler: false,
      title: '',
      start: '',
      end: '',
      id: '',
      events: [
        {
          id: '4QhmRwHwwrgFqXULXNtx4d',
          title  : 'event1',
          start  : '2018-06-08',
        },
        {
          id: '4QhmRwHwwrgFqXULXNerty',
          title  : 'event2',
          start  : '2018-06-05',
          end    : '2018-06-07',
        },
        {
          id: 'zehmRwHwwrgFqXULXNerty',
          title  : 'event3',
          start  : '2018-06-09T12:30:00',
          allDay : false,
        },
        {
          id: '4QhmPrEtQrgFqXULXNerty',
          title  : 'hop',
          start  : '2018-06-13T11:50:30'
        },
      ],
      config: {
        locale: 'fr',
        minTime: "07:00:00",
        maxTime: "21:00:00",
      },
    }
  },
  components: {
    FullCalendar
  },
  mounted () {
    let now = Date.now() + 24*60*60*1000
    this.start = this.format(now, true);
    this.end = this.format(now, true);
  },
  methods: {
    saveEvent(id) {
      if (id) {
        this.events.map(function (event) {
          if (event.id === id) {
            event.title = this.title
            event.start = this.format(this.start)
            event.end = this.format(this.end)
          }
          return event;
        })
      } else {
        let start = this.format(this.start)
        let end = this.end === this.start ? '' : this.format(this.end)
        this.events.push({id: tokgen.generate(), title: this.title, start: start, end: end})
      }
      this.eventHandler = false;
      this.title = '';
      this.refreshEvents();
    },
    complete (number) {
      return number > 9 ? number : '0' + number;
    },
    format (date, display) {
      if (typeof date === 'number') {
        date = new Date(date)
      }
      if (typeof date === 'string' && date.indexOf('T') === -1) {
        date = date.replace(' ', 'T')
        date = new Date(date)
        this.addEventShow = false;
      }

      return date.getFullYear() + '-' + this.complete(date.getMonth() + 1) + '-' + this.complete(date.getDate()) + (display ? ' ' : 'T') + this.complete(date.getHours()) + ':' + this.complete(date.getMinutes());
    },
    refreshEvents() {
      this.$refs.calendar.$emit('reload-events')
    },
    editEvent(a, b, c, d){
      let event = this.event.filter(data => data.id === a.id)
      this.title = event.title
      this.start = this.format(event.start, true)
      this.end = this.format(event.end, true)
      this.showEventHandler();
    },
    eventDrop(a, b, c, d){
      console.log('eventDrop')
      
    },
    eventResize(a, b, c, d){
      console.log('eventResize')
      
    },
    eventReceive(a, b, c, d){
      console.log('eventReceive')
      
    },
    eventCreated(a, b, c, d){
      this.showEventHandler();
    },
    eventRender(a, b, c, d){
      console.log('eventRender')
      
    },
    dayClick(a, b, c, d){
      console.log('dayClick')
      
    },
    showEventHandler(param, hide) {
      if (hide) {
        this.title = ''
      }
      if (param) {
        if (param.allDay) {
          let day = this.format(param.start._d, true).substring(0, 11)
          this.start = this.format(day + '07:00', true)
          this.end = this.format(day + '21:00', true)
        } else {
          this.start = this.format(param.start._d -2*60*60*1000, true)
          this.end = this.format(param.end._d -2*60*60*1000, true)
        }
      }
      this.eventHandler = !hide
      if (!hide) {
        this.$nextTick(function () {
          this.$refs.title.focus()
        })
      }
    }
  }
}
</script>

<style>
    @import '~fullcalendar/dist/fullcalendar.css';
</style>
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.fc-scroller{
  height:auto!important;
}
</style>

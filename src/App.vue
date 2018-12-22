<template>
  <div id="app">
    <div class="form" v-if="form">
      <h3>Add Event {{date}}</h3>
        <div class="form-group">
          <label for="name">Title</label>
          <input type="hidden" :value="date" >
          <input type="text" required v-model="event.title" name="name" id="name">
        </div>
        <div class="form-group">
          <label for="time">Time</label>
          <input type="time" required v-model="event.time" name="time" id="time">
        </div>
        <div class="form-group">
          <label for="email">Email</label>
          <input type="email" required v-model="event.email" name="email" id="email">
        </div>
        <div class="form-group">
          <label for="email">Color</label>
          <input type="color" required v-model="event.color" value="#5a88ca" name="color" id="color">
        </div>

        <button
          class="btn btn-success"
          @click="addEvent"
        > Add Event
        </button>
    </div>
    <full-calendar :events="events"
      defaultView="month"
      @event-selected="eventSelected"
      @event-created="eventCreated"
      @event-render="eventRender"
      @day-click="dayClick"
      @event-afer-render="eventAfterRender"
      @remove-events="removeEvents"
      ref="calendar"
      :config="config"
    >
    </full-calendar>
  </div>
</template>

<script>
// import kalender from '@/components/Calender.vue'
import { FullCalendar } from 'vue-full-calendar'
import 'fullcalendar/dist/fullcalendar.css'
export default {
  name: 'app',
  components: {
    FullCalendar
  },
  data() {
    return {
      config: {
        eventLimit: 3
      },
      form: false,
      date: '',
      event: {
        title: '',
        time: '',
        email: '',
        start: '',
        color: '',
      },
      defaultView: 'month',
      events: [
        // {
        //     title  : 'event1',
        //     start  : '2018-12-21',
        // },
        // {
        //     title  : 'event2',
        //     start  : '2010-01-05',
        //     end    : '2010-01-07',
        // },
        // {
        //     title  : 'event3',
        //     start  : '2010-01-09T12:30:00',
        //     allDay : false,
        // },
      ]
    }
  },
  methods: {

    eventSelected(event, jsEvent){
      alert("email " + event.description);
    },
    eventAfterRender(event, element, view) {
      alert('halo' +view);
    },
    eventCreated(event) {
      this.form=true;
    },

    dayClick(date, jsEvent, view) {
      this.date = date.format();
    },
    addEvent() {
      const eventss= {
        title: this.event.time + " " + this.event.title,
        description: this.event.email,
        start: this.date,
        color: this.event.color,
      };


      if (this.event.title === "") {
        alert('Title must be filled');
        return;
      }
      else if (this.event.time === "") {
        alert('time must be filled');
        return;
      }
      if (this.event.color === "#ffffff") {
        alert('color must be filled');
        return;
      }

      this.$http.post('https://vuejs-60074.firebaseio.com/data.json', eventss)
        .then(response =>{
          alert('data saved');
          this.$http.get('https://vuejs-60074.firebaseio.com/data.json')
          .then(response=> {
            return response.json()
          })
          .then(data=> {
            const resultArray =  [];
              for (let key in data){
                  resultArray.push(data[key]);
              }
              this.events = resultArray;
              console.log(resultArray);
          })
        },
        error =>{
          console.log(error);
      });
    },
  },
  created() {
    this.$http.get('https://vuejs-60074.firebaseio.com/data.json')
    .then(response=> {
      return response.json()
    })
    .then(data=> {
      const resultArray =  [];
      for (let key in data){
        resultArray.push(data[key]);
      }
      this.events = resultArray;
      console.log(resultArray);
    });
  },

};
</script>
<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}


.form {
  width: 50%;
  margin: 0 auto;

  &-group {
    display: flex;

    label {
      flex: 1;
      font-weight: bold;
    }
    input {
      flex: 1;
    }

  }
    button {
      width: 20%;
      font-size: 16px;
      color: white;
      margin-top: 20px;
      margin-bottom: 40px;
      background-color: #5a88ca;
      // width: 80%;
      right: 0;
    }
}

</style>

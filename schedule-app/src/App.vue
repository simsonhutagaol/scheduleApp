
<template>
  <div id="app">
    <div class="digital-clock">
      <h1>Scheduler App 📝 {{ currentTime }}</h1>
    </div>
    <div class="input-group">
      <input v-model="newEvent" placeholder="Add new event">
      <input type="time" v-model="newTime" placeholder="Select time">
      <button @click="addEvent" style="border-radius: 10px;">Add Event</button>
      <div class="share-container">
        <button class="share-button" v-if="events.length > 0" @click="shareScreenshot">
          📸
        </button>
        <span class="tooltip">Take Screenshot</span>
      </div>
    </div>
    
    <div class="empty-state">
      <p v-if="events.length === 0" class="fade-in">There are no events yet</p>
      <img v-if="events.length === 0" src="./assets/list.gif" alt="No events" class="empty-gif"/>
    </div>
    
    <table v-if="events.length > 0" class="schedule-table">
      <thead>
        <tr>
          <th>No</th>
          <th>Time</th>
          <th>Event</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(event, index) in events" :key="index" :class="{'completed': event.completed}">
          <td>{{ index + 1 }}</td>
          <td>{{ event.time }}</td>
          <td>{{ event.name }}</td>
          <td>
            <button @click="toggleCompletion(index)" style="background-color: transparent;">
              {{ event.completed ? '✅' : '☑️' }}
            </button>
            <button @click="removeEvent(index)" style="border-radius: 10px;">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>





<script>
import html2canvas from 'html2canvas';

export default {
  name: 'App',
  data() {
    return {
      newEvent: '',
      newTime: '',
      events: [],
      currentTime: this.formatTime(new Date()),
      imageUrl: '' // URL gambar untuk diunduh
    };
  },
  methods: {
    addEvent() {
      if (this.newEvent.trim() === '' || this.newTime === '') return;
      this.events.push({
        name: this.newEvent.trim(),
        time: this.newTime,
        completed: false
      });
      this.newEvent = '';
      this.newTime = '';
    },
    removeEvent(index) {
      this.events.splice(index, 1);
    },
    toggleCompletion(index) {
      this.events[index].completed = !this.events[index].completed;
    },
    updateClock() {
      this.currentTime = this.formatTime(new Date());
    },
    formatTime(date) {
      return date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', second: '2-digit' });
    },
    shareScreenshot() {
      html2canvas(document.querySelector("#app")).then(canvas => {
        // Create a link element
        const link = document.createElement('a');
        link.href = canvas.toDataURL('image/png');
        link.download = 'screenshot.png';
        link.click();
      }).catch(() => {
        alert('Failed to capture screenshot.');
      });
    }
  },
  mounted() {
    setInterval(this.updateClock, 1000); 
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
  padding: 0 10px;
}

.input-group {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center; 
  margin-bottom: 20px;
}

input {
  padding: 10px;
  font-size: 16px;
  margin: 5px;
  width: calc(100% - 20px); 
  max-width: 300px; 
}

button {
  padding: 8px 16px;
  font-size: 14px;
  background-color: #2c3e50;
  color: white;
  border: none;
  cursor: pointer;
  margin: 2px;
}

.digital-clock {
  margin: 20px 0;
  font-size: 18px;
}

.schedule-table {
  width: 100%;
  border-collapse: collapse;
  margin: 0 auto;
  table-layout: fixed; 
}

.schedule-table th, .schedule-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: center;
  word-wrap: break-word; 
  overflow-wrap: break-word;
}

.schedule-table th {
  background-color: #f4f4f4;
}

.schedule-table tr {
  transition: background-color 0.3s ease;
}

.schedule-table tr:hover {
  background-color: #f1f1f1;
}

.schedule-table tr.completed {
  background-color: rgb(195, 98, 98); 
  opacity: 0.5;
}

.schedule-table tr:not(.completed) {
  background-color: rgb(138, 199, 138); 
}

.fade-in {
  animation: fadeIn 0.5s ease-in;
}

.share-button {
  padding: 8px 16px;
  font-size: 40px;
  background-color:transparent;
  color: white;
  border: none;
  cursor: pointer;
  margin-left: 10px; 
  border-radius: 10px;
  position: relative; 
}

.share-container {
  position: relative; 
  display: flex;
  align-items: center; 
}

.tooltip {
  display: none; 
  position: absolute;
  background-color: #333;
  color: #fff;
  padding: 5px;
  border-radius: 4px;
  bottom: 100%; 
  left: 50%;
  transform: translateX(-50%);
  white-space: nowrap;
  z-index: 1;
}

.share-button:hover + .tooltip {
  display: block; 
  opacity: 0.8;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}


@media (max-width: 600px) {
  .digital-clock {
    font-size: 16px;
  }

  .input-group {
    flex-direction: column; 
    margin-bottom: 10px;
  }

  input {
    font-size: 14px;
    width: calc(100% - 20px);
    max-width: none;
  }

  button {
    font-size: 12px;
    padding: 6px 12px;
  }

  .schedule-table th, .schedule-table td {
    font-size: 12px;
    padding: 6px;
  }

  .empty-state {
    margin-top: 20px;
  }

  .empty-gif {
    width: 50%;
    height: auto;
  }
}
</style>





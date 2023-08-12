<template>
  <div class="container">
    <!-- @side-bar -->
    <div v-if="isSidebarOpen" class="container-sidebar" :class="{ 'closed': !isSidebarOpen }">
        <h1>Hello Bong</h1>
        <ul class="sidebar-content">
          <li v-for="(item, index) in sidebarItems" :key="index">
            <span class="content-title" @click="handleClick(index)" :class="{ active: activeIndex === index }">
              <i :class="item.icon"></i>
              {{ item.title }}
              <i class="fa fa-chevron-down"></i>
            </span>

            <ul class="list inner" :class="{ slow: activeIndex === index }" v-if="activeIndex === index">
              <div class="content">
                <li v-for="(subItem, subIndex) in item.subItems" :key="subIndex" class="sub-item">
                  {{ subItem }}
                </li>
              </div>
            </ul>
          </li>
        </ul>
    </div>
    <!-- @tabe-calendar -->
    <div class="container-main" :style="{ width: isSidebarOpen ? 'calc(100% - 250px)' : '100%' }">
      <FullCalendar :options="calendarOptions" />
      <div class="other-element" :class="{ 'expanded': isSidebarOpen }">
        <!-- ... Content of the other element ... -->
      </div>
    </div>
    <!-- @drag N Drop -->
    <div class="drag-drop">
      
    </div>

     <!-- Button to toggle the side-bar -->
     <button class="toggle-button" @click="toggleSidebar">
        <h1>X</h1>
      </button>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
import FullCalendar from '@fullcalendar/vue3'
import timeGridPlugin from '@fullcalendar/timegrid'
import interactionPlugin from '@fullcalendar/interaction'
import { INITIAL_EVENTS, createEventId } from './event-utils'

export default defineComponent({
  components: {
    FullCalendar,
  },
  data() {
    return {
      sidebarItems: [
        {
          title: 'Acedemy Year',
          icon: 'fa fa-home',
          subItems: [
            '2022 - 2023', 
            '2023 - 2024', 
            '2025 - 2025'
          ]
        },
        {
          title: 'Department',
          icon: 'fa fa-cog',
          subItems: [
            'GIC',
            'GCA',
            'GTR'
          ]
        },
        {
          title: 'Levels',
          icon: 'fa fa-envelope',
          subItems: [
            'Engeener',
            'Degree',
            'Master'
          ]
        },
        {
          title: 'Options',
          icon: 'fa fa-users',
          subItems: [
            'Food Technology',
            'Crush Technology',
            'Sl-Ke Alone Technology'
          ]
        },
        {
          title: 'Years',
          icon: 'fa fa-users',
          subItems: [
            'Year one',
            'Year two',
            'Year three',
            'Year Four',
            'Year Five'
          ]
        },
        {
          title: 'Semesters',
          icon: 'fa fa-users',
          subItems: [
            'Semester I',
            'Semester II'
          ]
        },
        {
          title: 'Groups',
          icon: 'fa fa-users',
          subItems: [
            'A',
            'B',
            'C'
          ]
        }
      ],
      activeIndex: null,
      calendarOptions: {
        plugins: [
          timeGridPlugin,
          interactionPlugin // needed for dateClick
        ],
        headerToolbar: {
          right: 'timeGridWeek'
        },
        initialView: 'timeGridWeek', // Set the initial view to week
        initialEvents: INITIAL_EVENTS,
        editable: true,
        selectable: true,
        selectMirror: true,
        weekends: true,
        select: this.handleDateSelect,
        eventClick: this.handleEventClick,
        eventsSet: this.handleEvents,

        // Hide the Sunday column
        hiddenDays: [0], // 0 corresponds to Sunday

        // Adjust the visible hours
        slotMinTime: '07:00:00 ', // Set the minimum time to 7 AM
        slotMaxTime: '18:00:00 ', // Set the maximum time to 5 PM
        // Change the time format to display minutes
        slotLabelFormat: {
          hour: 'numeric',
          minute: '2-digit',
          omitZeroMinute: false,
          meridiem: 'short'
        },
        // Remove the row of all-day slot names
        allDaySlot: false,
        views:{
          timeGridWeek: {
            dayHeaderContent: this.customDayHeaderContent
          }
        },
      },
      currentEvents: [],
      isSidebarOpen: true // Initial state of the side-bar
    }
  },
  methods: {
    // Custom function to format day header labels
    customDayHeaderContent(args) {
      const date = new Date(args.date);
      const day = date.toLocaleDateString('en-US', { weekday: 'long' }); // Change 'long' to 'short' if you prefer abbreviated names
      return day;
    },
    toggleSidebar() {
      this.isSidebarOpen = !this.isSidebarOpen;
    },
    handleWeekendsToggle() {
      this.calendarOptions.weekends = !this.calendarOptions.weekends // update a property
    },
    handleDateSelect(selectInfo) {
      let title = prompt('Please enter a new title for your event')
      let calendarApi = selectInfo.view.calendar

      calendarApi.unselect() // clear date selection

      if (title) {
        calendarApi.addEvent({
          id: createEventId(),
          title,
          start: selectInfo.startStr,
          end: selectInfo.endStr,
          allDay: selectInfo.allDay
        })
      }
    },
    handleEventClick(clickInfo) {
      if (confirm(`Are you sure you want to delete the event '${clickInfo.event.title}'`)) {
        clickInfo.event.remove()
      }
    },
    handleEvents(events) {
      this.currentEvents = events
    },
    handleClick(index) {
      if (this.activeIndex === index) {
        this.activeIndex = null;
      } else {
        this.activeIndex = index;
      }
    }
    
  },
  
})
</script>


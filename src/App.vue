<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "006",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "005",
          "name": "Split-Knuckle Haymaker",
          "status": "success"
        },
        {
          "slug": "004",
          "name": "Rallying Cry",
          "status": "success"
        },
        {
          "slug": "003",
          "name": "Floodgate",
          "status": "success"
        },
        {
          "slug": "002",
          "name": "Vigilant Gaze",
          "status": "success"
        },
        {
          "slug": "001",
          "name": "Bug-Hunt",
          "status": "success"
        }
      ],
      "pilots": [
        {
          "callsign": "Tzadek",
          "alias": "Tzadek",
          "code": "ANANKE-CLASS 05883671-6035-4990-8d79-9873968457e0",
          "corpro": "HA",
          "frame": "Sunzi",
          "mech": "Solomon In His Glory"
        },
        {
          "callsign": "Scorpion",
          "alias": "Roline Eischen",
          "code": "Eischen.Roline:03d7f4a3-9e53-48b1-8250-16305bd963d1//NDL-C-STOLEN-ORBIT",
          "corpro": "ERROR",
          "frame": "UNKNOWN",
          "mech": "Wall of Memories MkII"
        },
        {
          "callsign": "Southwest",
          "alias": "Sweet William",
          "code": "William.Sweet:2ff431e5-3780-48ab-a243-bf9ee075e3d9//NDL-C-EPSILON-FEW",
          "corpro": "HA",
          "frame": "Sherman",
          "mech": "Hemlock"
        },
        {
          "callsign": "Switchboard",
          "alias": "Nova Sanwijs",
          "code": "Sanwijs.Nova:73e59c11-e560-4e66-bd1c-6f51bb8470b2//NDL-C-DEEP-STATION",
          "corpro": "ERROR",
          "frame": "UNKNOWN",
          "mech": "Cheerful Oblivion"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes",
        "gate": "N/A",
        "ring": "Atlas Line",
        "headerTitle": "SERAL-19",
        "headerSubtitle": "",
        "subheaderTitle": "FIRST RESPONSE TEAM",
        "subheaderSubtitle": "Situation Assessment",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>

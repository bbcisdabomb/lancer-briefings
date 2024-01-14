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
      "mission_slug": "003",
      "current_md": "",
      "events": "001",
      "missions": [
        {
          "slug": "003",
          "name": "Dustgrave",
          "status": "start"
        },
		{
		  "slug": "002",
		  "name": "Daybreak",
		  "status": "success",
		},
		{
		  "slug": "001",
		  "name": "Solstice Rain",
		  "status": "success",
		},
      ],
      "pilots": [
        {
          "callsign": "Psyche",
          "alias": "Carmel Crick",
          "code": "6a4c478c-eb8d-4ef5-90e5-08b1566e75fb///PLR-C-DEEP-STATION//5318ead8-d1fb-4ea5-8429-9b180530cfe5",
          "corpro": "HORUS",
          "frame": "Goblin",
          "mech": "All You Never Imagined"
        },
        {
          "callsign": "Quasar",
          "alias": "Harald Bogdanski",
          "code": "6439d31d-1ce1-4629-8e34-01bd508f3c16///PLR-C-DEEP-STATION//480707b3-c7c3-487c-9e8e-2e324d6cffc6",
          "corpro": "SSC",
          "frame": "Monarch",
          "mech": "Collateral Damage"
        },
        {
          "callsign": "Galahad",
          "alias": "Darren Price",
          "code": "cf0199d7-c292-4a5a-9f2c-daa7309bc1c5///PLR-C-DEEP-STATION//1435d694-3929-4a0c-bf8b-53f45ad3ba2a",
          "corpro": "IPS-N",
          "frame": "Vlad",
          "mech": "Looking For Trouble"
        },
        {
          "callsign": "Basilisk",
          "alias": "Zander Codex",
          "code": "d76a7763-ff77-4352-8089-d2e55a9d8b8f///PLR-C-DEEP-STATION//dc45d0e6-9e4f-4e94-8fb0-1df0fa108ea8",
          "corpro": "IPS-N",
          "frame": "Tortuga",
          "mech": "Catastrophe"
        },
        {
          "callsign": "Midas",
          "alias": "Kennedy Fairchild",
          "code": "7db65fff-dc42-4010-949f-876217df7833///NDL-C-DEEP-STATION//cb198c65-5e51-42bf-9ce8-2f4a4f292997",
          "corpro": "IPS-N",
          "frame": "Raleigh",
          "mech": "Rest For The Wicked"
        },
      ],
      "header": {
        "planet": "Havelburg",
        "year": "5018u",
        "system": "Iarite-VII",
        "gate": "Quantum Product",
        "ring": "Delta-Line",
        "headerTitle": "Mythos",
        "headerSubtitle": "Mercenary Company",
        "subheaderTitle": "Where Needed",
        "subheaderSubtitle": "Delta-Echo-Echo-Zulu",
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

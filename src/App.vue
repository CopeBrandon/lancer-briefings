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
      "mission_slug": "a01.1",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "a01.1",
          "name": "Feet First",
          "status": "success"
        }
      ],
      "pilots": [
        {
          "callsign": "Bluejay",
          "alias": "Scott Darian",
          "code": "65fceeb6-f7a2-4317-bf35-690678fcc9a4//NDL-C-BETA-MUSE",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Sparrow"
        },
        {
          "callsign": "Dell",
          "alias": "Conagher",
          "code": "5a9c408c-2ce1-42c1-839a-f92cc710f1d3//NDL-C-SINGULARITY-HAMMER",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "After The Water, The Clouds"
        },
        {
          "callsign": "Disco",
          "alias": "Viktor 'Vik' Maarschalkerweerd",
          "code": "66aee2fc-be69-4d89-9f2a-4c4251bc4de5//NDL-C-NULL-TEMPLE",
          "corpro": "GMS",
          "frame": "Chomolungma",
          "mech": "Malicious Intent Too"
        },
        {
          "callsign": "The Tower",
          "alias": "Kazuki",
          "code": "e58bac44-ac53-43f2-bed0-acceccffd173//NDL-C-GOLD-SEPTEMBER",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "A Profound Problem"
        }

      ],
      "header": {
        "planet": "A01",
        "year": "12 s.d.",
        "system": "A0[Arch Industries]",
        "gate": "Ann.-Arch Worlds",
        "ring": "Annamite Line",
        "headerTitle": "Ganter Industries",
        "headerSubtitle": "",
        "subheaderTitle": "Industrial Concern",
        "subheaderSubtitle": "Annamite Subdivision",
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

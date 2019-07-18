<template>
  <div class="events">
    <v-container>
      <v-flex xs12 md5 offset-md7>
        <v-text-field
          v-model="search"
          append-icon="search"
          label="検索"
          single-line
          hide-details
          class="my-4"
        />
      </v-flex>
      <v-expansion-panel>
        <v-expansion-panel-content>
          <template v-slot:header>
            <h2 class="title">カテゴリで絞り込む</h2>
          </template>
          <v-layout row wrap class="pa-2">
            <div v-for="tag in tags" :key="`tag-chip-${tag}`">
              <v-btn
                round
                :color="selectTag === tag ? '#90caf9' : 'grey'"
                @click="changeSelectTag(tag)"
              >
                {{ tag }}
              </v-btn>
            </div>
          </v-layout>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-container>

    <v-container grid-list-xl>
      <v-data-iterator
        :items="events"
        :rows-per-page-items="rowsPerPageItems"
        content-class="layout row wrap"
        :search="search"
        :custom-filter="eventFilter"
      >
        <template v-slot:item="props">
          <v-flex xs12 md6>
            <EventCard :data="props.item" />
          </v-flex>
        </template>
      </v-data-iterator>
    </v-container>
  </div>
</template>

<script>
import axios from 'axios'
import EventCard from './EventCard'

/*
【埼玉県】広報情報 - データセット - Open Data Saitama
https://opendata.pref.saitama.lg.jp/data/dataset/9b283d97-f53e-4346-b734-b43695eac1c9
*/
const dataURLorg = 'http://www.pref.saitama.lg.jp/opendata/event_rdf.xml'

var getProxyURL = function(url) {
  var host = "fukuno.jig.jp"
  //const cnv = "xml2json"
  const cnv = ""
  var base = "https://" + host + "/proxy/ITqT5WkhCf2yn1s9?cnv=" + cnv;
  //var url2 = base + "&cache=no&callback=" + getCallbackMethod(callback) + "&url=" + encodeURI(url);
  var url2 = base + "&cache=no&url=" + encodeURI(url)
  //jsonp(url2);
  return url2
}
const dataURL = getProxyURL(dataURLorg)
//console.log(dataURL)

// https://www.pref.saitama.lg.jp/a0301/kouhoujouhou-data.html

// https://www.pref.saitama.lg.jp/a0301/documents/datasheet.pdf
const getGenre = function(n) {
  const GENRE = [ "ニュース", "イベント", "学習・体験", "講座・講演", "資格取得", "募集", "お知らせ" ]
  const res = GENRE[n]
  if (res)
    return res
  return GENRE[0]
}

const convert = function(data) {
  var parser = new DOMParser()
  var dom = parser.parseFromString(data, 'text/xml')
  var items = dom.querySelectorAll('channel > item')
  let res = []
  //console.log(items)
  let tags = {}

  items.forEach(function(it) {
    let text = {}
    it.childNodes.forEach(function(n) {
      text[n.nodeName] = n.textContent
      tags[n.nodeName] = n.nodeName
    })
    const dom2 = parser.parseFromString("<d>" + text['description'] + "</d>", 'text/xml')
    const items2 = dom2.querySelectorAll('d > *')
    items2.forEach(function(n) {
      text[n.nodeName] = n.textContent
      tags[n.nodeName] = n.nodeName
    })
    //console.log(text['genre']) // 数値？

    //console.log(it)
    let d = {
      "event_name": text['title'],
      "description": text['outline'],
      "remarks":"",
      "category": getGenre(text['genre']),
      "start_date": text["date_time"], //text["dc:date"],
      "end_date": "",
      "contact": text['inquery'], //text["dc:creater"],
      "contact_phone_number": "",
      "event_place": text['place'],
      "event_place_url": "",
      "link": text["link"],
      "address": "",
      "mail_address": "",
      "transportation": "",
      "image": text['image_file']
    }
    res.push(d)
  })
  //console.log(tags)
  /*
    #text: "#text"
    data_id: "data_id"
    date_time: "date_time"
    dc:creator: "dc:creator"
    dc:date: "dc:date"
    dc:subject: "dc:subject"
    description: "description"
    genre: "genre"
    image_file: "image_file"
    inquiry: "inquiry"
    link: "link"
    map: "map"
    outline: "outline"
    place: "place"
    publish_end: "publish_end"
    publish_start: "publish_start"
    title: "title"
  */
//  console.log(res)
  return res
}


export default {

  components: {
    EventCard
  },

  data() {
    return {
      rowsPerPageItems: [10, 30, {"text":"$vuetify.dataIterator.rowsPerPageAll","value":-1}],
      events: [],
      search: '',
      tags: [],
      selectTag: ''
    }
  },

  created() {
    const self = this
    axios.get(dataURL).then(res => {
      self.events = convert(res.data)
      this.tags = this.events.map((d) => [d.category])
        .reduce((a, b) => a.concat(b))
        .filter((x, i, self) => self.indexOf(x) === i)
    })
  },

  methods: {
    eventFilter(items, search) {
      const filter = (val, search) => {
        return (
          val != null &&
          typeof val !== 'boolean' &&
          (val.event_name.toString() + val.description.toString() + val.remarks.toString())
            .toLowerCase().indexOf(search) !== -1
        )
      }
      if (this.selectTag === '') return items.filter(v => filter(v, search))
      return items.filter(v => v.category === this.selectTag & filter(v, search))
    },

    changeSelectTag(tag) {
      this.selectTag = (this.selectTag === tag) ? '' : tag
    }
  }

}
</script>

<style>

</style>

<template lang="html">
<div>
  <h1>Our Energy Tracker</h1>
  <h3> {{fromDate}} to {{toDate}} </h3>

  <GChart type="PieChart" :data="main" :options="chartOptions" id="chart"/>

  <p> {{fromDate}} </p>
  <p> {{toDate}} </p>
</div>
</template>

<script>
export default {
  data() {
    return {
      main: [],
      generationMix: [],
      timeFrom: "",
      fromDate: null,
      timeTo: "",
      toDate: null,
      keys: [],
      values: [],
      chartOptions: {
          'title': 'Energy Percentage Tracker',
          'titleTextStyle': {
            'color': 'white'
          },
          'is3D': true,
          'width': 800,
          'height': 600,
          'backgroundColor': 'transparent',
          'legend': {
            'alignment': 'center',
            'textStyle': {
              'color': 'white'
            }
          }
      }
    }
  },
  mounted() {
    fetch('https://api.carbonintensity.org.uk/generation')
    .then(res => res.json())
    .then(data => {
      this.generationMix = data['data']['generationmix'];
      this.timeFrom = data['data']['from'];
      this.timeTo = data['data']['to'];
      this.findKeys();
      this.findValues();
      this.getMain();
      this.getTimes();
    })

  },
  methods: {
    findKeys: function() {
    if (this.generationMix[0]) {
      this.keys.push(Object.keys(this.generationMix[0]))
      return this.keys.flat();
    }
  },
  findValues: function() {
    for (const element of this.generationMix) {
      this.values.push(Object.values(element))
    }
    return this.values;
  },
  getMain: function() {
    this.main.push(this.keys.flat());
   for (const pair of this.values) {
     this.main.push(pair);
   }
    return this.main;
  },
  getTimes: function() {
    this.fromDate = new Date(this.timeFrom);
    this.toDate = new Date(this.timeTo);
    this.fromDate = this.fromDate.toUTCString();
    this.toDate = this.toDate.toUTCString();
  }
  }
}
</script>

<style lang="css" scoped>
h1 {
  display: flex;
  justify-content: center;
  color: white;
}

h3 {
  display: flex;
  justify-content: center;
  color: white;
}

#chart {
  display: flex;
  justify-content: center;
  margin-top: -20px;
  background: transparent;
}
</style>

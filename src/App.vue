<template>
  <v-app>
    <v-content>
      <v-layout>
        <v-data-table
          :items="journey"
          :headers="journeyHeaders"
          sort-by="day"
          :sort-desc="true"
        >
          <template v-slot:top>
            <v-toolbar flat color="white">
              <v-toolbar-title>Point Journey</v-toolbar-title>
              <v-spacer></v-spacer>
              <v-btn color="primary" @click="save">New Day</v-btn>
<!--              <v-dialog v-model="dialog" max-width="500px">-->
<!--                <template v-slot:activator="{ on }">-->
<!--                  <v-btn color="primary" dark class="mb-2" v-on="on">New Day</v-btn>-->
<!--                </template>-->
<!--                <v-card>-->
<!--                  <v-card-title>-->
<!--                    <span class="headline">Point Allocation</span>-->
<!--                  </v-card-title>-->

<!--                  <v-card-text>-->
<!--                    <v-container>-->
<!--                      <v-row>-->
<!--                        <v-col cols="12" sm="6">-->
<!--                          <v-text-field type="number" min="0" v-model.number="newDay.weightTraining" label="Weight Training"></v-text-field>-->
<!--                        </v-col>-->
<!--                        <v-col cols="12" sm="6">-->
<!--                          <v-text-field type="number" min="0" v-model.number="newDay.cardio" label="Cardio"></v-text-field>-->
<!--                        </v-col>-->
<!--                        <v-col cols="12" sm="6">-->
<!--                          <v-text-field type="number" min="0" v-model.number="newDay.mobility" label="Mobility"></v-text-field>-->
<!--                        </v-col>-->
<!--                        <v-col cols="12" sm="6">-->
<!--                          <v-text-field type="number" min="0" v-model.number="newDay.gymnastics" label="Gymnastics"></v-text-field>-->
<!--                        </v-col>-->
<!--                      </v-row>-->
<!--                    </v-container>-->
<!--                  </v-card-text>-->

<!--                  <v-card-actions>-->
<!--                    <v-spacer></v-spacer>-->
<!--                    <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>-->
<!--                    <v-btn color="blue darken-1" text @click="save">Save</v-btn>-->
<!--                  </v-card-actions>-->
<!--                </v-card>-->
<!--              </v-dialog>-->
            </v-toolbar>
          </template>

          <template v-slot:item.athleteLevel="{ item }">
            {{ getAthleteLevel(item.totalGainPoints) }}
          </template>

          <template v-slot:item.weightTrainingPoints="{ item }">
            {{ item.weightTrainingPoints }} ({{ getCategoryLevel(item.weightTrainingPoints) }}Lv)
          </template>

          <template v-slot:item.cardioPoints="{ item }">
            {{ item.cardioPoints }} ({{ getCategoryLevel(item.cardioPoints) }}Lv)
          </template>

          <template v-slot:item.mobilityPoints="{ item }">
            {{ item.mobilityPoints }} ({{ getCategoryLevel(item.mobilityPoints) }}Lv)
          </template>

          <template v-slot:item.gymnasticsPoints="{ item }">
            {{ item.gymnasticsPoints }} ({{ getCategoryLevel(item.gymnasticsPoints) }}Lv)
          </template>
        </v-data-table>

        <v-card style="position: sticky;top: 0;" width="500">
          <v-card-title>Settings</v-card-title>
          <v-card-text>
            <v-col>
              <v-text-field type="number" min="0" v-model.number="pointsPerDay" label="Points Per Day"></v-text-field>

              <v-card>
                <v-card-title>Default Point Allocation</v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6">
                        <v-text-field type="number" min="0" v-model.number="defaultNewDay.weightTraining" label="Weight Training"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6">
                        <v-text-field type="number" min="0" v-model.number="defaultNewDay.cardio" label="Cardio"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6">
                        <v-text-field type="number" min="0" v-model.number="defaultNewDay.mobility" label="Mobility"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6">
                        <v-text-field type="number" min="0" v-model.number="defaultNewDay.gymnastics" label="Gymnastics"></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>
              </v-card>

              <v-text-field class="mt-4" v-model="athleteLevelSchemeText" label="Athlete Level Scheme"></v-text-field>

              <v-text-field v-model="categoryLevelSchemeText" label="Category Level Scheme"></v-text-field>
            </v-col>
          </v-card-text>
        </v-card>
      </v-layout>
    </v-content>
  </v-app>
</template>

<script>
export default {
  name: 'App',

  data: () => ({
    dialog: false,

    journeyHeaders: [
      {
        text: 'Day',
        value: 'day',
      },
      {
        text: 'Athlete Level',
        value: 'athleteLevel',
        sortable: false,
      },
      {
        text: 'Total Gain Pts',
        value: 'totalGainPoints',
        sortable: false,
      },
      {
        text: 'Weight Training Pts',
        value: 'weightTrainingPoints',
        sortable: false,
      },
      {
        text: 'Cardio Pts',
        value: 'cardioPoints',
        sortable: false,
      },
      {
        text: 'Mobility Pts',
        value: 'mobilityPoints',
        sortable: false,
      },
      {
        text: 'Gymnastics Pts',
        value: 'gymnasticsPoints',
        sortable: false,
      },
    ],
    journey: [
      {
        day: 0,
        athleteLevel: 1,
        totalGainPoints: 0,
        weightTrainingPoints: 0,
        cardioPoints: 0,
        mobilityPoints: 0,
        gymnasticsPoints: 0,
      }
    ],

    pointsPerDay: 5,

    newDay: {
      weightTraining: 0,
      cardio: 0,
      mobility: 0,
      gymnastics: 0,
    },

    defaultNewDay: {
      weightTraining: 0,
      cardio: 0,
      mobility: 0,
      gymnastics: 0,
    },

    athleteLevelSchemeText: '30,60,90,150',
    categoryLevelSchemeText: '1,1,1,3,3,3,4,5,6',
  }),

  computed: {
    athleteLevelScheme () {
      return this.athleteLevelSchemeText.split(',').map(i => parseInt(i))
    },

    categoryLevelScheme () {
      return this.categoryLevelSchemeText.split(',').map(i => parseInt(i))
    }
  },

  watch: {
    'defaultNewDay.weightTraining' (newValue, oldValue) {
      if (this.getSumPointOfObject(this.defaultNewDay) > this.pointsPerDay) {
        this.$nextTick(() => {
          this.defaultNewDay.weightTraining = oldValue
        })
        return
      }

      this.newDay = JSON.parse(JSON.stringify(this.defaultNewDay))
    },

    'defaultNewDay.cardio' (newValue, oldValue) {
      if (this.getSumPointOfObject(this.defaultNewDay) > this.pointsPerDay) {
        this.$nextTick(() => {
          this.defaultNewDay.cardio = oldValue
        })
        return
      }

      this.newDay = JSON.parse(JSON.stringify(this.defaultNewDay))
    },

    'defaultNewDay.mobility' (newValue, oldValue) {
      if (this.getSumPointOfObject(this.defaultNewDay) > this.pointsPerDay) {
        this.$nextTick(() => {
          this.defaultNewDay.mobility = oldValue
        })
        return
      }

      this.newDay = JSON.parse(JSON.stringify(this.defaultNewDay))
    },

    'defaultNewDay.gymnastics' (newValue, oldValue) {
      if (this.getSumPointOfObject(this.defaultNewDay) > this.pointsPerDay) {
        this.$nextTick(() => {
          this.defaultNewDay.gymnastics = oldValue
        })
        return
      }

      this.newDay = JSON.parse(JSON.stringify(this.defaultNewDay))
    },

    'newDay.weightTraining' (newValue, oldValue) {
      if (this.getSumPointOfObject(this.newDay) > this.pointsPerDay) {
        this.$nextTick(() => {
          this.newDay.weightTraining = oldValue
        })
      }
    },

    'newDay.cardio' (newValue, oldValue) {
      if (this.getSumPointOfObject(this.newDay) > this.pointsPerDay) {
        this.$nextTick(() => {
          this.newDay.cardio = oldValue
        })
      }
    },

    'newDay.mobility' (newValue, oldValue) {
      if (this.getSumPointOfObject(this.newDay) > this.pointsPerDay) {
        this.$nextTick(() => {
          this.newDay.mobility = oldValue
        })
      }
    },

    'newDay.gymnastics' (newValue, oldValue) {
      if (this.getSumPointOfObject(this.newDay) > this.pointsPerDay) {
        this.$nextTick(() => {
          this.newDay.gymnastics = oldValue
        })
      }
    },
  },

  methods: {
    save () {
      if (this.getSumPointOfObject(this.newDay) === 0) {
        alert('Please allocate something')
        return
      }

      const totalAddedPoints = this.newDay.gymnastics + this.newDay.weightTraining + this.newDay.mobility + this.newDay.cardio
      const lastDay = this.journey[this.journey.length - 1]

      this.journey.push({
        day: this.journey.length,
        athleteLevel: 1,
        totalGainPoints: lastDay.totalGainPoints + totalAddedPoints,
        weightTrainingPoints: lastDay.weightTrainingPoints + this.newDay.weightTraining,
        cardioPoints: lastDay.cardioPoints + this.newDay.cardio,
        mobilityPoints: lastDay.mobilityPoints + this.newDay.mobility,
        gymnasticsPoints: lastDay.gymnasticsPoints + this.newDay.gymnastics,
      })

      this.close()
    },

    close () {
      this.dialog = false
      setTimeout(() => {
        this.newDay = Object.assign({}, this.defaultNewDay)
      }, 300)
    },

    getSumPointOfObject(object) {
      let points = 0

      for (let key in object) {
        points += object[key]
      }

      return points
    },

    getAthleteLevel(points) {
      let level = 1

      this.athleteLevelScheme.forEach(expect => {
        if (points >= expect) {
          level++

          points -= expect
        }
      })

      return level
    },

    getCategoryLevel(points) {
      let level = 1

      this.categoryLevelScheme.forEach(expect => {
        if (points >= expect) {
          level++

          points -= expect
        }
      })

      return level
    },
  }
};
</script>

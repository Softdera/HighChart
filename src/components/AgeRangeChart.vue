<!-- AgeRangeChart.vue -->
<template>
    <div>

        <highcharts :options="chartOptions"></highcharts>

        <highcharts :options="agerange"></highcharts>


    </div>
  </template>
  
  <script>
  import { Chart } from "highcharts-vue";
  import axios from "axios";
  import 'firebase/database';

  export default {

    components: {
        highcharts: Chart,
        axios,  
    },
    
    data() {
      return {
      
      chartOptions: {
        chart: {
          type: "bar",
        },

        title: {
          text: "Blood Group Distribution",
        },

        xAxis: {
          title: {
            text: "Blood Group",
          },
          categories: ["A+", "A-", "B+", "B-", "AB+", "AB-", "O+", "O-"],
        },

        yAxis: {
          title:{
            text: "Number of People",
          },
        },

        series: [
          {
            name: "Number of People",
            data: [], //number of people falling under a blood group
          },
        ],
      },

      // data for secondchart
      agerange: {
        chart: {
          type: "bar",
        },

        title: {
          text: "Age Range",
        },

        xAxis: {
          title: {
            text: "Age Range",
          },

          

          categories: [ "Children (1-12)", "Teens (13-17)", "Youths (18-34)", "Adults (35 and above)"],
        },

        yAxis: {
          title: {
            text: "Number of People",
          },
        },

        series: [
          {
            name: "Number of People",
            data: [], 
          },
        ],
      },
    }; 
    },


    mounted() {
    this.fetchDataFromDatabase();
  },
  methods: {
    fetchDataFromDatabase() {
      // Check if data exists in local storage
      const localStorageData = localStorage.getItem('bloodGroupsData');

      if (localStorageData) {
        // If data exists in local storage, use it
        const bloodGroupsData = JSON.parse(localStorageData);
        this.updateChart(bloodGroupsData);
      } else {
        // If data doesn't exist in local storage, fetch from Firebase
        axios.get( "https://heel-9ca35-default-rtdb.firebaseio.com/People.json")
          .then(response => {
            const data = response.data;
            if (data) {
              const bloodGroupsData = this.groupBloodGroups(data);
              // Store data in local storage
              localStorage.setItem('bloodGroupsData', JSON.stringify(bloodGroupsData));
              this.updateChart(bloodGroupsData);
            }
          })
          .catch(error => {
            console.error('Error fetching data:', error);
          });
      }
    },
    groupBloodGroups(data) {
      const counts = {
        'A+': 0,
        'A-': 0,
        'B+': 0,
        'B-': 0,
        'AB+': 0,
        'AB-': 0,
        'O+': 0,
        'O-': 0
      };

      // Iterate over the fetched data and count the occurrences of each blood group
      Object.values(data).forEach(person => {
        counts[person.bloodGroup]++;
      });

      return counts;
    },
    updateChart(data) {
      this.chartOptions.xAxis.categories = Object.keys(data);
      this.chartOptions.series[0].data = Object.values(data);
      //this.chartOptions.series[0].data = Object.values(person.bloodGroup); 

    }
  }













    
   /* mounted() {
        axios
        .get(
            "https://heel-9ca35-default-rtdb.firebaseio.com/People.json"
        )
        .then((response) => {
          // Storing fetched data in localStorage by storing it as an object
          localStorage.setItem("PeopleData", JSON.stringify(response.data));

          //retrieving it from local storage
          let bloodGroupData = localStorage.getItem("PeopleData");

          //converting and returning it back as an object
          let convertedBloodGroupData = JSON.parse(bloodGroupData);
          this.arrangeData(convertedBloodGroupData);

        }).catch(error => {
          console.error('Error fetching data:', error);
        });
        /*axios.get('YOUR_FIREBASE_DATABASE_URL/bloodGroups.json')
        .then(response => {
          const data = response.data;
          if (data) {
            const bloodGroupsCount = this.groupBloodGroups(data);
            this.chartOptions.xAxis.categories = Object.keys(bloodGroupsCount);
            this.chartOptions.series[0].data = Object.values(bloodGroupsCount);
            this.$refs.chart.chart.update(this.chartOptions);
    },
        
  /*methods: {
    arrangeData(data) {
      //array containing preformatted values of data 
      let bloodGroup = [];

      //array that will contain formatted values of data for only field bloodgroup 
      let newBloodGroup = [];

      //array that will contain formatted values of data for only field age 
      let Age = [];

      //pushing formatted data from the request made 
      for (let key in data) {
        bloodGroup.push({ ...data[key] });
      }

      //formatting it and selecting only the bloodgroup and age  
      for (let k in bloodGroup) {
        //This will contain values for the blood group in the data returned 
        newBloodGroup.push(bloodGroup[k].values);

        //This will contain values for the age of the people in the data returned 
        Age.push(bloodGroup[k].Age);
      }

      //setting our bloodgroup to return unique values 
      let c = 0;
      let start = false;

      for (let i = 0; i < newBloodGroup.length; i++) {
        for (let j = 0; j < this.chartOptions.xAxis.categories.length; j++) {
          if (newBloodGroup[i] == this.chartOptions.xAxis.categories[j]) {
            start = true;
          }
        }
        c++;

        if (c == 1 && start == false) {
          this.chartOptions.xAxis.categories.push(newBloodGroup[i]);
        }
        start = false;
        c = 0;
      }

      //count number of bloodgroup that repeated = to the number of people that has that same blood group
      let number_of_ppl_for_bloodgroupcount = {};

      for (let counter of newBloodGroup) {
        if (number_of_ppl_for_bloodgroupcount[counter]) {
          number_of_ppl_for_bloodgroupcount[counter] += 1;
        } else {
          number_of_ppl_for_bloodgroupcount[counter] = 1;
        }
      }

      //move set containing the number of bloodgroup that repeated into the data array for bar chart
      this.chartOptions.series[0].data = Object.values(number_of_ppl_for_bloodgroupcount); 

      //Filter to return totalcount of people that are between the ages of 1-12 = Children

      let oneToTwelve = Age.filter((obj) => {
        if (obj <= 1 || obj <= 12) {
          return true;
        }

        return false;
      }).length;

      //Filter to return totalcount of people that are between the ages of 13-17 = Teens
      let thirteenToSeventeen = Age.filter((obj) => {
        if (obj >= 13 || obj <= 17)
        {
            return true;
        }
            return false;
      }).length

      //Filter to return totalcount of people that are between the ages of 18-29 = Young Adult
      let eighteenToThirtyFour = Age.filter((obj) => {
        if (obj >= 18 && obj <= 34) {
          return true;
        }

        return false;
      }).length;

      //Filter to return totalcount of people that are between the ages of 30 upwards = Adult*
      let thrithyFiveUpWards = Age.filter((obj) => {
        if (obj >= 35) {
          return true;
        }

        return false;
      }).length;

      //Push the returned number of people for each age group
       
      this.agerange.series[0].data.push(
        oneToTwelve,
        thirteenToSeventeen,
        eighteenToThirtyFour,
        thrithyFiveUpWards
      );
    },*/
  

  










  
}     
  

    
  
  
  </script>
  
  <style scoped>

  </style>
  
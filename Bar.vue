<script>
import { Bar } from "vue-chartjs";
import Activity from "@/data/activity.json";

export default {
  extends: Bar,
  data() {
    return {
      jsonActivityLists: Activity.data,

      chartData: [],
      chartWeek: [],
      chartMonthName: "",
      color: ""
    };
  },
  computed: {
    dt() {
      const date = new Date();
      return this.chartMonthName + " " + date.getDate();
      // return this.chartMonthName + " " + date.getMonth() + " " + date.getDate();
    },
    chartDatas() {
      // console.log(this.$store.getters.getChartData);
      return this.$store.getters.getChartData;
    },
    chartWeeks() {
      // let weekDaysArray = this.$store.getters.getChartWeek;
      // let fdow = weekDaysArray[0];
      // let ldow = weekDaysArray[weekDaysArray.length];
      // return `${this.chartMonthName}`;
      // return this.chartMonthName;
      return null;
    },
    chartMonthNamess() {
      // console.log(this.$store.getters.getChartMonthName);
      return this.$store.getters.getChartMonthName;
    }
  },
  created() {
    this.chart();
  },
  mounted() {
    // Overwriting base render method with actual data.
    this.renderChart({
      display: false,
      scales: {
        xAxes: [
          {
            gridLines: {
              color: "rgba(0, 0, 0, 0)"
            }
          }
        ],
        yAxes: [
          {
            gridLines: {
              color: "rgba(0, 0, 0, 0)"
            }
          }
        ]
      },
      // labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
      labels: ["S", "M", "T", "W", "T", "F", "S"],
      datasets: [
        {
          label: this.dt,
          backgroundColor: this.color,
          data: this.chartData
        }
      ]
    });
  },

  // props: ['color'],
  methods: {
    chart() {
      const weekDaysRequests = [];
      const weekDays = [];

      const listMonthName = new Date(
        this.jsonActivityLists[0].createdAt
      ).toLocaleDateString("en-US", {
        month: "short"
      });

      this.jsonActivityLists.forEach((list) => {
        const listDay = parseInt(new Date(list.createdAt).getDate());
        if (
          weekDaysRequests[new Date(list.createdAt).getDay()] === undefined ||
          weekDaysRequests[new Date(list.createdAt).getDay()] === null
        ) {
          weekDaysRequests[new Date(list.createdAt).getDay()] = 1;
          weekDays[listDay] = 1;
        } else {
          weekDays[listDay] = weekDays[listDay] + 1;
          weekDaysRequests[new Date(list.createdAt).getDay()] =
            weekDaysRequests[new Date(list.createdAt).getDay()] + 1;
        }
      });

      this.chartData = weekDaysRequests;
      this.chartWeek = weekDays;
      this.chartMonthName = listMonthName;
      this.color = "#57C1A6";
    }
  }
};
</script>

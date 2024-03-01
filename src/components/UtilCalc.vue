<template>
  <v-container class="content d-flex justify-center">
    <v-responsive max-width="1000px" class="fill-height px-4 custom-width">
      <v-row class="d-flex justify-center my-5">
        <p class="title">Util Calculator</p>
      </v-row>
      <v-row v-for="(utility, index) in utilities" :key="index">
        <v-col>
          <v-text-field v-model="utility.name" :placeholder="'Enter Utility name'" class="input-field"></v-text-field>
        </v-col>
        <v-col>
          <v-text-field v-model="utility.amount" :placeholder="'Enter ' + utility.name + ' amount'"
            class="input-field"></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-btn size="large" @click="addUtilityField" block>Add Utility</v-btn>
        </v-col>
        <v-col>
          <v-btn size="large" @click="generate" block>Generate</v-btn>
        </v-col>

      </v-row>
      <v-row>
        <v-col>
          <v-card class="mt-3" v-if="result">
            <v-card-title>Results</v-card-title>
            <hr class="my-2">
            <v-card-text>
              <h2 v-for="transfer in result" :key="transfer.from + transfer.to">
                {{ transfer.from }} pays ${{ transfer.amount }} to {{ transfer.to }}
                <div class="my-4" />
              </h2>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
  <v-footer class="copyright" border>Copyright © 2024 Campbell Frost</v-footer>
</template>

<script>
export default {
  data() {
    return {
      utilities: [{ name: 'Gas', amount: null }, { name: 'Electric', amount: null }, { name: 'Internet', amount: null }],
      result: null,
    };
  },
  methods: {
    generate() {
      const total = this.utilities.reduce((acc, utility) => acc + parseFloat(utility.amount || 0), 0);
      const average = total / this.utilities.length;
      const aboveAverage = [];
      const belowAverage = [];

      this.utilities.forEach(utility => {
        if (parseFloat(utility.amount || 0) > average) aboveAverage.push({ name: utility.name, amount: parseFloat(utility.amount || 0) });
        else belowAverage.push({ name: utility.name, amount: parseFloat(utility.amount || 0) });
      });

      const amountsToTransfer = [];
      aboveAverage.forEach(above => {
        let difference = above.amount - average;
        belowAverage.forEach(below => {
          if (below.amount < average) {
            const transferAmount = Math.min(difference, average - below.amount);
            if (transferAmount > 0) {
              amountsToTransfer.push({
                from: below.name,
                to: above.name,
                amount: transferAmount.toFixed(2),
              });
              difference -= transferAmount;
            }
          }
        });
      });

      this.result = amountsToTransfer;
    },
    addUtilityField() {
      const newUtility = { name: 'New Utility', amount: null };
      this.utilities.push(newUtility);
    }
  },
};
</script>

<style>
.custom-width {
  max-width: 1200px;
}

.title {
  padding-top: 50px;
  font-size: 50px;
}

.v-messages {
  min-height: 0px;
}

.v-input__details {
  display: none;
}

.content {
  min-height: calc(100vh - 100px);
}

.copyright {
  margin-top: 59px;
}
</style>

<template>
  <v-container>
    <v-responsive class="align-center text-center fill-height px-4 custom-width">
      <v-row class="d-flex justify-center mb-3">
        <p class="title">Util Calculator</p>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field v-model="gas" placeholder="Enter Gas amount" class="input-field"></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field v-model="electric" placeholder="Enter Electric amount" class="input-field"></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field v-model="internet" placeholder="Enter Internet amount" class="input-field"></v-text-field>
        </v-col>
      </v-row>
      <v-row>
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
                {{ transfer.from }} pays {{ transfer.amount }} to {{ transfer.to }}
                <div class="my-4" />
              </h2>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      gas: null,
      electric: null,
      internet: null,
      result: null,
    };
  },
  methods: {
    generate() {
      const total = parseFloat(this.gas || 0) + parseFloat(this.electric || 0) + parseFloat(this.internet || 0);
      const average = total / 3;
      const aboveAverage = [];
      const belowAverage = [];

      if (parseFloat(this.gas || 0) > average) aboveAverage.push({ name: 'Gas', amount: parseFloat(this.gas || 0) });
      else belowAverage.push({ name: 'Gas', amount: parseFloat(this.gas || 0) });

      if (parseFloat(this.electric || 0) > average) aboveAverage.push({ name: 'Electric', amount: parseFloat(this.electric || 0) });
      else belowAverage.push({ name: 'Electric', amount: parseFloat(this.electric || 0) });

      if (parseFloat(this.internet || 0) > average) aboveAverage.push({ name: 'Internet', amount: parseFloat(this.internet || 0) });
      else belowAverage.push({ name: 'Internet', amount: parseFloat(this.internet || 0) });

      const amountsToTransfer = [];
      aboveAverage.forEach(utility => {
        let difference = utility.amount - average;
        belowAverage.forEach(below => {
          if (below.amount < average) {
            const transferAmount = Math.min(difference, average - below.amount);
            if (transferAmount > 0) {
              amountsToTransfer.push({
                from: below.name,
                to: utility.name,
                amount: transferAmount.toFixed(2),
              });
              difference -= transferAmount;
            }
          }
        });
      });

      this.result = amountsToTransfer;
    },
  },
};
</script>

<style>

.custom-width{
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
</style>

<template>
  <v-container class="content d-flex justify-center">
    <v-responsive max-width="1000px" class="fill-height px-4 custom-width">
      <v-row align="center mt-5">
        <v-col>
          <p class="title">Util Calculator</p>
        </v-col>
        <v-col class="d-flex justify-end">
          <v-switch inset class="pt-4" color="info" v-model="darkMode" @change="toggleTheme()"
            :label="`Change to ${darkMode ? 'Dark' : 'Light'} mode`"></v-switch>
        </v-col>
      </v-row>

      <v-row v-for="(utility, index) in utilities" :key="index">
        <v-col>
          <v-text-field v-model="utility.name" :placeholder="'Enter Utility name'" class="input-field"></v-text-field>
        </v-col>
        <v-col>
          <v-text-field v-model="utility.amount" :placeholder="'Enter ' + utility.name + ' amount'" class="input-field"
            prepend-inner-icon="mdi-currency-usd"></v-text-field>
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
        <v-col class="pb-4">
          <v-card class="mt-3" v-if="result">
            <v-card-title>Results</v-card-title>
            <hr class="my-2">
            <v-card-text>
              <h2 v-for="transfer in result" :key="transfer.from + transfer.to">
                {{ transfer.from }} pays ${{ transfer.amount }} to {{ transfer.to }}
                <div class="my-4"></div>
              </h2>
            </v-card-text>
            <hr>
            <v-card-text>
              <h2>Cost Per Person: ${{ (total / utilities.length).toFixed(2) }}</h2>
            </v-card-text>
          </v-card>

        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
  <v-footer class="copyright" border>
    Copyright © 2024 Campbell Frost
  </v-footer>
</template>

<script>
import { ref } from 'vue';
import { useTheme } from "vuetify";

export default {
  setup() {
    const utilities = ref([
      { name: 'Gas', amount: null },
      { name: 'Electric', amount: null },
      { name: 'Internet', amount: null },
      { name: 'Water', amount: null }
    ]);
    const result = ref(null);
    let total = ref(0);

    const theme = useTheme();
    const darkMode = ref(false);

    const generate = () => {
      total.value = utilities.value.reduce((acc, utility) => acc + parseFloat(utility.amount || 0), 0);
      const average = total.value / utilities.value.length;
      const aboveAverage = [];
      const belowAverage = [];

      utilities.value.forEach(utility => {
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

      result.value = amountsToTransfer;
    };

    const addUtilityField = () => {
      const newUtility = { name: 'New Utility', amount: null };
      utilities.value.push(newUtility);
    };

    const toggleTheme = () => {
      theme.global.name.value = darkMode.value ? "light" : "dark";
    }

    return {
      utilities,
      result,
      generate,
      addUtilityField,
      total, 
      toggleTheme,
      darkMode,
    };
  }
};
</script>

<style>
.custom-width {
  max-width: 1200px;
}

.title {
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
  margin-top: 58px;
}
</style>

<template>
  <div :class="classNames" v-if="cards && cards.length">
    <template v-for="(card, idx) in cards">
      <span
        class="separator my-2"
        v-if="idx === 1 && reverse"
        :key="card.name + idx"
      ></span>
      <Card :hiddenLogo="hiddenLogo" :reverse="reverse" :key="card.title + card.name+idx" v-bind="card" />
      <span
        class="separator my-2"
        v-if="idx === 1 && reverse"
        :key="card.title + idx"
      ></span>
    </template>
  </div>
  <div v-else>...loading</div>
</template>

<script>
import Card from "../card/Card.component";
import {
  MANAGEMENT_QUERY,
  COMPANY_QUERY,
  MARKETING_QUERY
} from "./card.queries";

export default {
  name: "CardContainer",
  components: {
    Card
  },
  data() {
    return {
      cards: [],
      classNames: "card-container flex justify-around my-5",
      reverse: true,
      hiddenLogo: false
    };
  },
  props: {
    cardType: String
  },
  async mounted() {
    let query;

    switch (this.cardType) {
      case "company-data":
        query = COMPANY_QUERY;
        this.classNames += "";
        break;
      case "marketing-data":
        query = MARKETING_QUERY;
        this.classNames += " marketing-style rounded-lg -mt-8";
        this.hiddenLogo = true;
        break;
      default:
        query = MANAGEMENT_QUERY;
        this.reverse = false;
        break;
    }
    const {
      data: { company }
    } = await this.$apollo.query({
      query
    });
    this.cards = Object.entries(company).reduce((acc, companyData) => {
      //TODO write the includes part transform string
      const title = `${companyData[0]}`;
      const name = `${companyData[1]}`;
      if (title !== "__typename") acc.push({ title, name });
      return acc;
    }, []);
  }
};
</script>

<style>
.card-container {
  width: 100%;
  max-width: 70vw;
  min-width: 500px;
}
.marketing-style {
  background: white;
  -webkit-box-shadow: -1px 10px 5px -6px rgba(0, 0, 0, 0.32);
  -moz-box-shadow: -1px 10px 5px -6px rgba(0, 0, 0, 0.32);
  box-shadow: -1px 10px 5px -6px rgba(0, 0, 0, 0.32);
}
.separator {
  border-right: 1px solid #e2e8f0;
}
</style>

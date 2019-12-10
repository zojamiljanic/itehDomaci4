<template>
  <div>
    <!-- @click odnosno v-on:click je Vue direktiva koja se okida kada se klike misem
      na element, addEventLister je analogni koncept u js-u.
      mi kada kliknemo pozivamo metodu iz methods objekta u script sekciji i prosledjujemo joj item,
      itemm koji dobijamo iz v-for direktive prolaskom kroz iterabilnu listu
    v-bind:key ili :key sluzi samo da jedinstveno identifikuje element u v-for-u
    :variant je primary, success, danger... bootstrap helper klasa
    -->
    <!-- v-for dirketiva sluzi da renderuje nesto sto je itreabilno bez kopiranja koda, npr. -->
    <!-- Pokazati ovaj primer kako radi: <div v-for="item in 5" :key="item.id">{{ item }}</div> -->
    <b-list-group-item
      style="cursor: pointer;"
      @click="changeStatus(item)"
      v-for="(item, id) in list.itemi"
      :key="id"
      id="list-item"
      class="d-flex justify-content-between align-items-center"
      :variant="(item.status == '0')?'info':'success'"
    >
      <!-- :syle ili v-bind:syle, sluzi da se u prop ubaci neki dinamicki content,
      npr. :style = '"color": stildugmeta', gde bi stilldugmeta bio neki prop ili data objekat-->
      <div
        :style="(item.status == '0')?'textDecoration: none;':'textDecoration: line-through'"
      >{{id + 1}}. {{item.content}}</div>
      <div>
        <!-- click.stop sluzi da spreci Event bubbling -->
        <b-badge @click.stop="deleteItem(item)" href="#" variant="danger" pill>X</b-badge>
      </div>
    </b-list-group-item>

    <b-input-group class="mt-3" style="z-index: 0" prepend="New item">
      <!-- v-model direktiva sluzi za 2way data binding, sve sto se unese u inputu, automatski
      je uneto u newListTitle data objektu, vazi i obrnuto-->
      <b-form-input v-model="newItem"></b-form-input>
      <b-input-group-append>
        <b-button @click="addItem(list)" size="sm" text="Add" variant="success">Add</b-button>
      </b-input-group-append>
    </b-input-group>
  </div>
</template>

<script>
export default {
  // Props, saljemo property-je iz roditelja u dete, ovo je dete komponenta
  props: {
    list: {
      type: Object
    }
  },
  // Data funkcija, sadrzi nase reaktivne objekte, svaka promena nad ovim objektima se automatski
  // reflektuje i u template
  data() {
    return {
      newItem: ""
    };
  },
  methods: {
    async deleteItem(item) {
      await this.$http.delete(`${this.$store.state.baseUrl}/itemi/${item.id}`);
      // Signal roditelju da se desio neki event
      this.$emit("update-lists");
      // VAZNO primetiti da se iz metoda mora sve pristupati preko this, jer Vue sve binduje na this
    },
    async changeStatus(item) {
      const resBody = {
        status: item.status == "0" ? "1" : "0",
        content: item.content,
        list_id: item.list_id
      };
      await this.$http.put(
        `${this.$store.state.baseUrl}/itemi/${item.id}`,
        resBody
      );
      // Signal roditelju da se desio neki event
      this.$emit("update-lists");
    },
    async addItem(list) {
      // Obicna validacija
      if (this.newItem.length < 1) return;
      const resBody = { content: this.newItem, status: 0, list_id: list.id };
      await this.$http.post(`${this.$store.state.baseUrl}/itemi`, resBody);
      this.newItem = "";
      // Signal roditelju da se desio neki event
      this.$emit("update-lists");
    }
  }
};
</script>

<style scoped>
</style>

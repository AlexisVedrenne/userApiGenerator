<template>
  <div>
    <div class="row justify-center q-mt-md">
      <p class="text-bold">{{ users.length }} users finded</p>
    </div>
    <div class="row justify-center">
      <q-btn flat @click="reset" color="negative" label="Reset filters" />
    </div>
    <div class="row justify-center q-mt-md">
      <q-select
        @update:model-value="filterNat"
        @input-value="filterNat"
        class="col-3"
        v-model="nat"
        label="Nationality"
        :options="options"
      />
      <q-checkbox
        :disable="male"
        @update:model-value="filterWoman"
        color="secondary"
        label="Woman"
        v-model="female"
      />
      <q-checkbox
        :disable="female"
        @update:model-value="filterMan"
        color="secondary"
        label="Man"
        v-model="male"
        class="q-mr-lg q-ml-sm"
      />
      <q-input
        v-model="search"
        @update:model-value="find"
        class="col-6"
        label="Search an user by first name"
      >
        <template v-slot:prepend>
          <q-icon color="primary" name="search" />
        </template>
      </q-input>
      <q-btn icon="add" class="q-mr-sm" color="secondary" nos-caps @click="add" round />
    </div>
    <div class="row q-col-gutter-md q-ma-sm">
      <q-intersection
        once
        v-for="(user, index) in users"
        :key="index"
        transition="scale"
        class="col-3"
      >
        <UserCard :user="user" />
      </q-intersection>
    </div>
  </div>
</template>

<script>
import UserCard from "components/UserCard.vue";
export default {
  components: {
    UserCard,
  },
  data() {
    return {
      nat: null,
      options: [],
      male: false,
      female: false,
      users: [],
      savedUsers: [],
      search: "",
      number: 12,
    };
  },
  mounted() {
    let res = this.http(this.number);
    res.then((user) => (this.savedUsers = user));
    res.then((users) => this.addOptions(users));
  },

  methods: {
    http(number) {
      let users = fetch("https://randomuser.me/api?results=" + number)
        .then((resp) => resp.json())
        .then(function (data) {
          let res = data.results;
          return res;
        })
        .catch(function (error) {
          console.log(error);
        });
      users.then((val) => (this.users = val));
      return users;
    },
    addOptions(users) {
      users.forEach((user) => {
        if (!this.options.includes(user.nat)) {
          this.options.push(user.nat);
        }
      });
    },
    add() {
      this.number += 10;
      let res = this.http(this.number);
      res.then((user) => (this.savedUsers = user));
      this.male = false;
      this.female = false;
    },
    find() {
      if (this.search !== "") {
        this.users = this.users.filter((user) => user.name.first.includes(this.search));
      } else if (this.female) {
        this.users = Object.values(this.savedUsers);
        this.users = this.users.filter((user) => user.gender != "male");
        return;
      } else if (this.male) {
        this.users = Object.values(this.savedUsers);
        this.users = this.users.filter((user) => user.gender === "male");
        return;
      } else {
        this.users = Object.values(this.savedUsers);
      }
    },
    filterWoman() {
      if (!this.female) {
        this.users = this.users.filter((user) => user.gender != "male");
      } else {
        this.users = Object.values(this.savedUsers);
      }
    },
    filterMan() {
      if (!this.male) {
        this.users = this.users.filter((user) => user.gender === "male");
      } else {
        this.users = Object.values(this.savedUsers);
      }
    },
    filterNat() {
      if (this.nat) {
        this.users = Object.values(this.savedUsers);
        this.users = this.users.filter((user) => user.nat == this.nat);
      }
    },
    reset() {
      this.users = Object.values(this.savedUsers);
      this.male = false;
      this.female = false;
      this.nat = null;
      this.search = "";
    },
  },
};
</script>

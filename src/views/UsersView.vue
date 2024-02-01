<template>
  <v-card flat title="Users">
    <template v-slot:[`text`]>
      <v-text-field
        v-model="search"
        label="Search"
        prepend-inner-icon="mdi-magnify"
        single-line
        variant="outlined"
        hide-details
      ></v-text-field>
    </template>

    <v-data-table
      :headers="headers"
      :items="filteredUsers"
      density="compact"
      hover="true"
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon
          size="small"
          class="me-2"
          @click="userInfoPage(item)"
        >
          mdi-account-arrow-right
        </v-icon>
        <v-icon
          size="small"
          class="me-2"
          @click="item && console.log(getMatchingAttribute(item))"
        >
          mdi mdi-filter-check
        </v-icon>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
import users from '../../public/users.json';

export default {
  data() {
    return {
      users: [],
      search: '',
      //attributes: ['firstName', 'lastName', 'email', 'age', 'gender'],
      //selectedAttributes: [],
      headers: [
        { title: 'Id', align: 'center', key: 'id' },
        { title: 'Firstname', align: 'center', key: 'firstName' },
        { title: 'Lastname', align: 'center', key: 'lastName' },
        { title: 'Age', align: 'center', key: 'age' },
        { title: 'Gender', align: 'center', key: 'gender' },
        { title: 'Actions', align: 'center', key: 'actions', sortable: false }
      ],
    };
  },
  created() {
    this.selectedAttributes = this.attributes;
    this.getUsersData();
  },
  computed: {
    filteredUsers() {
      return this.users.filter((user) => {
        return Object.values(user)
          .flatMap((value) => {
            if (typeof value === 'object') {
              return Object.values(value);
            } else {
              return value;
            }
          })
          .join(' ')
          .toLowerCase()
          .includes(this.search.toLowerCase());
      });
    },
  },
  methods: {
    getUsersData() {
      this.users = users;
    },
    getMatchingAttribute(item) {
      if (!item) {
        return [];
      }
      let matchingAttributes = [];
      for (let [key, value] of Object.entries(item)) {
        if (typeof value === 'object') {
          for (let [subKey, subValue] of Object.entries(value)) {
            if (subValue.toString().toLowerCase().includes(this.search.toLowerCase())) {
              matchingAttributes.push(`${key}.${subKey}`);
            }
          }
        } else if (value.toString().toLowerCase().includes(this.search.toLowerCase())) {
          matchingAttributes.push(key);
        }
      }
      return matchingAttributes;
    },
    userInfoPage(item) {
      this.$router.push({ name: 'userInfo', params: { id: item.id } });
    },
  },
};
</script>
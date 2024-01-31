<template>
    <v-card class="mx-auto" max-width="434" rounded="0">
        <v-avatar color="grey" size="150" rounded="0">
            <v-img cover :src="userInfo.image"></v-img>
        </v-avatar>
        <div>
            <v-list>
                <template v-for="(value, key) in userInfo">
                    <template v-if="typeof value === 'object'">
                        <v-list-item :key="key" :title="key">
                            <v-list-item-content>
                                <v-list-item-title>{{ value }}</v-list-item-title>
                            </v-list-item-content>
                            <v-list-item-action>
                                <v-list-item-group>
                                    <v-list-item v-for="(nestedValue, nestedKey) in value" :key="nestedKey"
                                        :title="nestedKey" :subtitle="nestedValue"></v-list-item>
                                </v-list-item-group>
                            </v-list-item-action>
                        </v-list-item>
                    </template>
                    <template v-else>
                        <v-list-item :key="key" :title="key" :subtitle="value"></v-list-item>
                    </template>
                </template>
            </v-list>
        </div>
    </v-card>
</template>

<script>
import users from '../../public/users.json';

export default {
    data() {
        return {
            users: [],
            userInfo: {},
        };
    },
    methods: {
        getUsersData() {
            this.users = users;
        },
        findUser() {
            this.userInfo = this.users.find((user) => {
                return user.id === parseInt(this.$route.params.id);
            });
        },
    },
    mounted() {
        this.getUsersData();
        this.findUser();
        console.log(this.userInfo);
    }
};
</script>
  
<template>
  <v-container fill-height>
    <v-row align="center" justify="center">
      <v-col md="4">
        <div v-if="currentUser || loginAsOther || !publicUsers">
          <h1 class="text-h4 mb-6 text-center">{{ $t('login') }}</h1>
          <login-form />
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue';
import { mapActions } from 'vuex';
import { UserDto } from '~/api';

export default Vue.extend({
  layout: 'fullpage',
  middleware: 'serverMiddleware',
  data() {
    return {
      loginAsOther: false,
      currentUser: {} as UserDto,
      publicUsers: [] as Array<UserDto>
    };
  },
  head() {
    return {
      title: this.$store.state.page.title
    };
  },
  created() {
    this.setPageTitle({ title: this.$t('login') });
  },
  async beforeMount() {
    try {
      this.publicUsers = (await this.$api.user.getPublicUsers({})).data;
    } catch (error) {
      console.error('Unable to get public users:', error);
    }
  },
  methods: {
    ...mapActions('page', ['setPageTitle'])
  }
});
</script>

<template>
    <v-app>
      <v-main>
        <PullBranch/>
      </v-main>
    </v-app>
</template>

<script>
import PullBranch from './components/PullBranch';

export default {
  name: 'App',

  components: {
    PullBranch,
  },

  data: () => ({
    //
  }),
};
</script>

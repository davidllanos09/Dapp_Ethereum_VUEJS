<template lang="pug">
#app
      section.hero.is-info.is-small
        .hero-head
          nav.navbar
            .container
              .navbar-brand
                a.navbar-item.text-black
                  span.vote-app Vote App
                span.navbar-burger(data-target='navbarMenuHeroA')
                  span
                  span
                  span
              #navbarMenuHeroA.navbar-menu
                .navbar-end
                  span.navbar-item
                    span.text-black {{ account }}
                  span.navbar-item(v-if="!isLogged")
                    a.button.is-info.is-inverted(@click="connect")
                      span.icon
                        font-awesome-icon(icon="wallet")
                      span Connect Wallet
        // Hero content: will be in the middle
        .hero-body.body
          .container.has-text-centered
            p.title.text-blue
              | Vote app
      .container.mt-16.body
        .columns
          .column.is-10.is-offset-1
            b-message.margin-top(type="is-warning" v-if="voteInfo.voted") You already voted
            b-field.margin-top(grouped)
              b-input(placeholder="Add proposal" v-model="name" expanded)
              p(class="control")
                b-button(label="Add" type="is-info" @click="addProposal")
            b-table(:data='proposals')
              b-table-column(field="name" label="Name" v-slot="props") {{ props.row.name }}
              b-table-column(field="votes" :centered="true" label="Votes" v-slot="props") {{ props.row.votesCount }}
              b-table-column(field="votes" :centered="true" label="Your vote" v-slot="props" v-if="voteInfo.voted")
                b-tag(type="is-info" v-if="props.index==voteInfo.vote") Your vote
              b-table-column(field="votes" :centered="true" label="Vote for" v-slot="props")
                b-button(type='is-info' @click="vote(props.index)" :disabled="voteInfo.voted") Vote
</template>
  
  <script>
  export default {
    name: 'App',
    data() {
      return {
        name: ""
      };
    },
    async mounted() {
      await this.$store.dispatch("connect");
      await this.$store.dispatch("getProposals");
      await this.$store.dispatch("voteInfo");
    },
    methods: {
      async connect() {
        await this.$store.dispatch("connect", 1);
      },
      async addProposal() {
        try {
          await this.$store.dispatch("addProposal", this.name);
          await this.$store.dispatch("getProposals");
        } catch (error) {
          this.$buefy.toast.open({
            message: "You need to be the admin to add proposals",
            type: 'is-danger'
          });
        }
      },
      async vote(index) {
        const res = await this.$store.dispatch("vote", index);
        if (res.isError) {
          this.$buefy.toast.open({
            message: res.msg,
            type: 'is-danger'
          });
        } else {
          this.$buefy.toast.open({
            message: "Success",
            type: 'is-success'
          });
        }
      }
    },
    computed: {
      voteInfo() {
        return this.$store.getters.voteInfo;
      },
      account() {
        return this.$store.getters.account;
      },
      isLogged() {
        return this.$store.getters.account === null ? false : true;
      },
      proposals() {
        return this.$store.getters.proposals;
      },
    }
  }
  </script>
  
  <style scoped>
  #app {
    background-color: #1d1d1d;
    color: #1d1d1d;
  }
  body {
    background-color: #000;
    color: #fff;
  }

  #back {
    background-color: #4a90e2 ;
  }
  
  .text-blue {
    color: #4a90e2;
  }

  .text-black {
    color: #000000;
  }
  
  .title {
    font-size: 2rem;
  }
  
  .margin-top {
    margin-top: 40px;
  }
  </style>
  
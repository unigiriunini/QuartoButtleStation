<template>
  <MatchList :matches=matches>Hi</MatchList>
</template>

<script>
export default {
  data() {
    return {
      matches: [],
      snapshots: []
    }
  },
  created() {
    this.snapshots.push(
      this.$fire.firestore.collection('GameRecords').onSnapshot((snapshot) => {
        this.matches = snapshot.docs.map((doc) => { return { id: doc.id, firstAI: doc.data().firstAI, secondAI: doc.data().secondAI, createdAt: doc.data().createdAt.toDate(), winner: doc.data().winner } }).sort((a, b) => b.createdAt - a.createdAt).map((match) => {
          match.createdAt = this.$moment(match.createdAt).format('YYYY/MM/DD HH:mm:ss')
          return match
        })
      })
    )
  },
  mounted() {
    window.addEventListener("beforeunload", this.unsubscribeSnapshots)
  },
  beforeDestroy() {
    this.unsubscribeSnapshots()
    window.removeEventListener("beforeunload", this.unsubscribeSnapshots)
  },
  methods: {
    unsubscribeSnapshots() {
      console.log(`Unsubscribe ${this.snapshots.length} snapshots`)
      this.snapshots.forEach((unsubscribe) => unsubscribe())
    }
  }
}
</script>

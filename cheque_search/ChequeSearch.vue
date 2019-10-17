<template>
  <div class="cheque-search">
    <cheque-search-filters
      class="cheque-filters"
      :query.sync="query"
    />
    <div class="cheque-table">
      <cheque-table
        :cheques="cheques"
        :num-rows="numRows"
        :query.sync="query"
        :loading="loading"
      />
    </div>
    <div class="selected-cheque">
      <customer-cheque
        v-if="selectedCheque != null"
        :cheque="selectedCheque"
      />
    </div>
  </div>
</template>

<script>
import { chequeService } from '@/service/cheque_search/chequeService'
import ChequeSearchFilters from '@/components/cheque_search/ChequeSearchFilters'
import ChequeTable from '@/components/cheque_search/ChequeTable'
import CustomerCheque from '@/components/cheque_search/CustomerCheque'

export default {
  name: 'ChequeSearch',
  components: {
    ChequeTable,
    ChequeSearchFilters,
    CustomerCheque,
  },
  data () {
    let today = new Date()
    let yesterday = new Date()
    yesterday.setDate(yesterday.getDate() - 1)
    return {
      searchResults: null,
      loading: false,
      query: {
        order: [{ dimension: 'open time', ascending: true, },],
        restaurantFilter: null,
        revenueCenterFilter: null,
        paymentAmountFilter: null,
        paymentTypeFilter: null,
        chequeVoidFilter: null,
        chequeNumberFilter: null,
        openTimeFilter: {
          dimension: 'open time',
          name: 'between',
          args: {
            start: yesterday.toISOString(),
            end: today.toISOString(),
          },
        },
        paging: {
          page: 0,
          pageSize: 12,
        },
      },
    }
  },
  asyncComputed: {
    selectedCheque () {
      if (this.selectedChequeId) {
        return chequeService.loadChequeDetails(this.$route.params.company_id, this.selectedChequeId)
      }
      return null
    },
  },
  computed: {
    selectedChequeId () {
      return this.$route.params.cheque_id
    },
    numRows () {
      if (this.searchResults == null) {
        return 0
      }
      return this.searchResults.paging.numRows
    },
    cheques () {
      if (this.searchResults == null) {
        return []
      }
      return this.searchResults.cheques
    },
  },
  watch: {
    query (value) {
      this.refreshSearchResults(value)
    },
  },
  mounted () {
    this.refreshSearchResults (this.query)
  },
  methods: {
    async refreshSearchResults (query) {
      this.loading = true
      let results = await chequeService.searchCheques(
        this.query,
        this.$route.params.company_id
      )
      if (query == this.query) {
        this.loading = false
        this.searchResults = results
      }
    },
  },
}
</script>

<style lang="scss" scoped>
.cheque-search {
  flex-grow: 1;
  display: flex;
  max-height: 100%;
  overflow: auto;
}

.cheque-filters {
  display: flex;
  flex-direction: column;
  background: $ingestOffwhite;
  width: 20vw;
  border-top-left-radius: 8px;
  z-index: 1;
  overflow: auto;
}

.selected-cheque {
  background: $darkGreen;
  width: 35vw;
  min-width: 35vw;
  padding: 24px;
  max-height: 100%;
  overflow: auto;
}
.cheque-table {
  flex-grow: 1;
  z-index: 1;
}

</style>

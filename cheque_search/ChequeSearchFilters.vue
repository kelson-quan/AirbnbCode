<template>
  <div class="cheque-search-filters">
    <ingest-button
      label="Restaurant"
      @click="openFilter = openFilter == 'restaurant' ? null : 'restaurant'"
    >
      <entity-filter-summary
        v-if="restaurantFilter != null"
        :filter="restaurantFilter"
      />
    </ingest-button>
    <vertical-slide :open="openFilter == 'restaurant'">
      <dimension-filter-select
        :dimension="restaurantDimension"
        :filter.sync="restaurantFilter"
      />
    </vertical-slide>
    <!-- Cheque number filter -->
    <ingest-button
      label="Check number"
      @click="openFilter = openFilter == 'chequeNumber' ? null : 'chequeNumber'"
    >
      <number-filter-summary
        v-if="chequeNumberFilter != null"
        :filter="chequeNumberFilter"
      />
    </ingest-button>
    <vertical-slide :open="openFilter == 'chequeNumber'">
      <dimension-filter-select
        :dimension="chequeNumberDimension"
        :filter.sync="chequeNumberFilter"
      />
    </vertical-slide>
    <!-- Payment Type Filter -->
    <ingest-button
      label="Payment type"
      @click="openFilter = openFilter == 'paymentMethod' ? null : 'paymentMethod'"
    >
      <entity-filter-summary
        v-if="paymentTypeFilter != null"
        :filter="paymentTypeFilter"
      />
    </ingest-button>
    <vertical-slide :open="openFilter == 'paymentMethod'">
      <dimension-filter-select
        :dimension="paymentMethodDimension"
        :filter.sync="paymentTypeFilter"
      />
    </vertical-slide>
    <!-- Payment amount Filter -->
    <ingest-button
      label="Payment amount"
      @click="openFilter = openFilter == 'paymentAmount' ? null : 'paymentAmount'"
    >
      <money-filter-summary
        v-if="paymentAmountFilter != null"
        :filter="paymentAmountFilter"
      />
    </ingest-button>
    <vertical-slide :open="openFilter == 'paymentAmount'">
      <dimension-filter-select
        :dimension="paymentAmountDimension"
        :filter.sync="paymentAmountFilter"
      />
    </vertical-slide>
    <!-- Revenue Center Filter -->
    <ingest-button
      label="Revenue center"
      @click="openFilter = openFilter == 'revenueCenter' ? null : 'revenueCenter'"
    >
      <entity-filter-summary
        v-if="revenueCenterFilter != null"
        :filter="revenueCenterFilter"
      />
    </ingest-button>
    <vertical-slide :open="openFilter == 'revenueCenter'">
      <dimension-filter-select
        :dimension="revenueCenterDimension"
        :other-filters="restaurantFilter == null ? [] : [restaurantFilter]"
        :filter.sync="revenueCenterFilter"
      />
    </vertical-slide>
    <!-- CheckVoidFilter -->
    <ingest-button
      label="Check void"
      @click="openFilter = openFilter == 'checkVoid' ? null : 'checkVoid'"
    >
      <boolean-filter-summary
        v-if="chequeVoidFilter != null"
        :filter="chequeVoidFilter"
      />
    </ingest-button>
    <vertical-slide :open="openFilter == 'checkVoid'">
      <dimension-filter-select
        :dimension="chequeVoidDimension"
        :filter.sync="chequeVoidFilter"
      />
    </vertical-slide>
  </div>
</template>

<script>
import IngestButton from '@/components/common/IngestButton'
import VerticalSlide from '@/components/common/VerticalSlide'
import DimensionFilterSelect from '@/components/controls/filter/DimensionFilterSelect'
import BooleanFilterSummary from '@/components/controls/filter/BooleanFilterSummary'
import MoneyFilterSummary from '@/components/controls/filter/MoneyFilterSummary'
import NumberFilterSummary from '@/components/controls/filter/NumberFilterSummary'
import EntityFilterSummary from '@/components/controls/filter/EntityFilterSummary'

export default {
  name: 'ChequeSearchFilters',
  components: {
    VerticalSlide,
    IngestButton,
    DimensionFilterSelect,
    BooleanFilterSummary,
    EntityFilterSummary,
    NumberFilterSummary,
    MoneyFilterSummary,
  },
  props: {
    query: {
      type: Object,
      required: true,
    },
  },
  data () {
    return {
      restaurantDimension: {type: 'entity', name: 'restaurant',},
      chequeNumberDimension: {type: 'number', name: 'check number',},
      chequeVoidDimension: {type: 'boolean', name: 'check void',},
      paymentMethodDimension: {type: 'entity', name: 'payment method',},
      paymentAmountDimension: {type: 'money', name: 'payment amount',},
      revenueCenterDimension: {type:'entity', name: 'revenue center',},
      openFilter: null,
    }
  },
  computed: {
    workDayFilter: {
      get () {
        return this.query.workDayFilter
      },
      set (workDayFilter) {
        this.$emit('update:query', { ...this.query, workDayFilter, })
      },
    },
    openTimeFilter: {
      get () {
        return this.query.openTimeFilter
      },
      set (openTimeFilter) {
        this.$emit('update:query', { ...this.query, openTimeFilter, })
      },
    },
    chequeNumberFilter: {
      get () {
        return this.query.chequeNumberFilter
      },
      set (chequeNumberFilter) {
        this.$emit('update:query', { ...this.query, chequeNumberFilter, })
      },
    },
    restaurantFilter: {
      get () {
        return this.query.restaurantFilter
      },
      set (restaurantFilter) {
        this.$emit('update:query', { ...this.query, restaurantFilter, })
      },
    },
    paymentTypeFilter: {
      get () {
        return this.query.paymentTypeFilter
      },
      set (paymentTypeFilter) {
        this.$emit('update:query', { ...this.query, paymentTypeFilter, })
      },
    },
    paymentAmountFilter: {
      get () {
        return this.query.paymentAmountFilter
      },
      set (paymentAmountFilter) {
        this.$emit('update:query', { ...this.query, paymentAmountFilter, })
      },
    },
    revenueCenterFilter: {
      get () {
        return this.query.revenueCenterFilter
      },
      set (revenueCenterFilter) {
        this.$emit('update:query', { ...this.query, revenueCenterFilter, })
      },
    },
    chequeVoidFilter: {
      get () {
        return this.query.chequeVoidFilter
      },
      set (chequeVoidFilter) {
        this.$emit('update:query', { ...this.query, chequeVoidFilter, })
      },
    },
  },
}
</script>

<style lang="scss" scoped>
.cheque-search-filters {
 z-index: 1;
}
.restaurant-filter {
  background: $ingestOffwhite;
}
.ingest-button {
  padding: 4 * $gridBase;
  box-shadow: inset 0px 3px 3px -3px rgba($darkGreen, .5);
}
.dimension-filter-select {
  height: 200px;
  width: 100%;
}
.vertical-slide {
  overflow: auto;
}
</style>

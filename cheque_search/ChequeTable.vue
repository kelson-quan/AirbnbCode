<template>
  <div class="cheque-table flex-column">
    <div class="header">
      <dimension-filter-select
        class="time-range"
        :dimension="{type: 'datetime', name:'open time'}"
        :filter.sync="openTimeFilter"
      />
    </div>
    <div class="table">
      <table
        id="table"
        class="table"
      >
        <!-- Table Headers -->
        <thead class="nowrap">
          <!-- Table Header Columns -->
          <th />
          <th @click="onDimensionClick('check number')">
            Check no.
            <v-icon
              v-if="isCurrentSort('check number', true)"
              name="chevron-down"
            />
            <v-icon
              v-if="isCurrentSort('check number', false)"
              name="chevron-up"
            />
          </th>
          <th @click="onDimensionClick('restaurant')">
            Restaurant name
            <v-icon
              v-if="isCurrentSort('restaurant', true)"
              name="chevron-down"
            />
            <v-icon
              v-if="isCurrentSort('restaurant', false)"
              name="chevron-up"
            />
          </th>
          <!-- check  -->
          <th @click="onDimensionClick('open time')">
            Open time
            <v-icon
              v-if="isCurrentSort('open time', true)"
              name="chevron-down"
            />
            <v-icon
              v-if="isCurrentSort('open time', false)"
              name="chevron-up"
            />
          </th>
          <th @click="onDimensionClick('payment amount')">
            Amount paid
            <v-icon
              v-if="isCurrentSort('payment amount', true)"
              name="chevron-down"
            />
            <v-icon
              v-if="isCurrentSort('payment amount', false)"
              name="chevron-up"
            />
          </th>
          <th />
        </thead>
        <!-- Table Content: List of Cheques -->
        <tbody class="">
          <busy-overlay :enabled="loading" />
          <tr
            v-for="(cheque, idx) in cheques"
            :key="idx"
            :class="{active: $route.params.cheque_id == cheque.id}"
            class=""
            @click="selectCheque(cheque)"
          >
            <td />
            <td data-title="Checknumber">
              {{ cheque.number }}
            </td>
            <td data-title="Restaurant">
              <div class="nowrap">
                {{ cheque.restaurant }}
              </div>
            </td>
            <td data-title="Time">
              <div class="nowrap">
                {{ formatOpenTime(cheque.openTime) }}
              </div>
            </td>
            <td data-title="Amount">
              {{ formatAmount(cheque.amount) }}
            </td>
            <td />
          </tr>
        </tbody>
      </table>
    </div>
    <b-pagination
      v-model="currentPage"
      class="page-bar"
      size="sm"
      limit="10"
      :label-last-page="'${rows / query.paging.pageSize}'"
      :total-rows="numRows"
      :per-page="query.paging.pageSize"
    />
  </div>
</template>

<script>

import { formatDate } from '@/utils/dateTimeUtils'
import { formatAmount } from '@/utils/moneyUtils'
import BusyOverlay from '@/components/common/BusyOverlay'
import DimensionFilterSelect from '@/components/controls/filter/DimensionFilterSelect'

export default {
  name: 'ChequeTable',
  components: {
    BusyOverlay,
    DimensionFilterSelect,
  },
  props: {
    query: {
      type: Object,
      required: true,
    },
    cheques: {
      type: Array,
      default: Array,
    },
    numRows: {
      type: Number,
      required: true,
    },
    loading: Boolean,
  },
  computed: {
    openTimeFilter: {
      get () {
        return this.query.openTimeFilter
      },
      set (openTimeFilter) {
        this.$emit('update:query', { ...this.query, openTimeFilter, })
      },
    },
    currentPage: {
      get () {
        return this.query.paging.page + 1
      },
      set (page) {
        if (page == null) {
          return
        }
        this.$emit('update:query', {...this.query, paging: {...this.query.paging, page: page - 1 ,},})
      },
    },
    payment: {
      get () {
        return this.query.payment
      },
      set (payment) {
        this.$emit('update:query', { ...this.query, payment, })
      },
    },
    revenueCenters: {
      get () {
        return this.query.revenueCenters
      },
      set (revenueCenters) {
        this.$emit('update:query', { ...this.query, revenueCenters, })
      },
    },
    isVoid: {
      get () {
        return this.query.isVoid
      },
      set (isVoid) {
        this.$emit('update:query', { ...this.query, isVoid, })
      },
    },
    order: {
      get () {
        return this.query.order
      },
      set (order) {
        this.$emit('update:query', { ...this.query, order, })
      },
    },
  },
  methods: {
    selectCheque (cheque) {
      this.$router.replace(
        `/company/${this.$route.params.company_id}/checks/${cheque.id}`)
    },
    formatOpenTime (dateTime) {
      return formatDate(dateTime)
    },
    formatAmount,
    // extend current sort
    isCurrentSort (dimensionName, asc) {
      return (
        this.order.length > 0 && this.order[0].dimension == dimensionName && this.order[0].ascending == asc
      )
    },
    onDimensionClick (dimensionName) {
      this.order = [
        {
          dimension: dimensionName,
          ascending: !this.isCurrentSort(dimensionName, true),
        },
        ...this.order.filter(field => field.dimension != dimensionName),
      ]
    },
  },
}
</script>

<style lang="scss" scoped>
.cheque-table {
  background-color: $lightGreen;
  height: 100%;
  display: flex;
  flex-direction: column;
  flex-grow: 1;
  box-shadow: inset 3px 0 3px -3px rgba($darkGreen, .5);
}
.table {
  overflow: auto;
  position: relative;
  margin: 0;
  flex-grow: 1;
}
.header {
  position: sticky;
  top: 0;
  z-index: 4;
  box-shadow: inset 3px 0 3px -3px rgba($darkGreen, .5);
  background-color: $white;
  padding: 24px;
  display: flex;
  justify-content: flex-end;
}
thead {
  background-color: $white;
  box-shadow: inset 3px 0 3px -3px rgba($darkGreen, .5);
}
tbody {
  height: calc(100% + -42px);
  overflow: hidden;
  cursor: pointer;
  flex-grow: 1;
  box-shadow: inset 0 3px 3px -3px rgba($darkGreen, .5);
}
table {
  height: 100%;
  max-height: 100%;
  flex-grow: 1;
}
tr {
  flex-grow: 1;
  height: 42px;
}
.cheque-table {
  thead, th, td {
    border-collapse: collapse;
    border: none;
  }
  th, td {
    padding: 12px;
    flex-grow: 1;
    text-align: right;
  }
}
.nowrap {
  white-space: nowrap;
}
tr:hover {
  background: rgba($darkGreen, .5);
}
tr.active {
  background: $darkGreen;
  color: $lightGreen;
}
.time-range {
  z-index: 3;
}
</style>

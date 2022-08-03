<template>
  <div class="p-1.5 w-full inline-block align-middle">
    <div class="overflow-hidden border rounded-lg">
      <table class="min-w-full divide-y divide-gray-200">
        <thead class="bg-gray-50">
        <tr>
          <th
            scope="col"
            class="
                    px-6
                    py-3
                    text-xs
                    font-bold
                    text-left text-gray-500
                    uppercase
                  "
          >
                  <span class="inline-flex items-center">
                    Account
                  </span>
          </th>
          <th
            scope="col"
            class="
                    px-6
                    py-3
                    text-xs
                    font-bold
                    text-left text-gray-500
                    uppercase
                  "
          >
                  <span class="inline-flex items-center">
                    Reference
                  </span>
          </th>
          <th
            scope="col"
            class="
                    px-6
                    py-3
                    text-xs
                    font-bold
                    text-left text-gray-500
                    uppercase
                  "
          >
                  <span class="inline-flex items-center">
                    Category
                  </span>
          </th>
          <th
            scope="col"
            class="
                    px-6
                    py-3
                    text-xs
                    font-bold
                    text-left text-gray-500
                    uppercase
                  "
          >
                  <span class="inline-flex items-center">
                    Date
                  </span>
          </th>
          <th
            scope="col"
            class="
                    px-6
                    py-3
                    text-xs
                    font-bold
                    text-left text-gray-500
                    uppercase
                  "
          >
                  <span class="inline-flex items-center">
                    Amount
                  </span>
          </th>
        </tr>
        </thead>
        <tbody class="divide-y divide-gray-200">
        <tr>
          <td class="px-6 py-4 text-sm text-gray-800 whitespace-nowrap">
            {{ transaction.accountName }}
          </td>
          <td class="px-6 py-4 text-sm text-gray-800 whitespace-nowrap" v-if="transaction.reference !== ''">
            {{ transaction.reference }}
          </td>
          <td class="px-6 py-4 text-sm text-gray-400 whitespace-nowrap" v-if="transaction.reference === ''">
            No reference provided
          </td>
          <td class="px-6 py-4 text-sm text-gray-800 whitespace-nowrap">
            {{ transaction.categoryName }}
          </td>
          <td class="px-6 py-4 text-sm text-gray-800 whitespace-nowrap">
            {{ formatDate(transaction.date) }}
          </td>
          <td class="px-6 py-4 text-sm text-gray-800 whitespace-nowrap">
            {{ transaction.amount }} <span class="text-gray-400">{{ transaction.currency }}</span>
          </td>
        </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
name: "TransactionDetail",
  data() {
    return {
      transaction: this.$route.params.transactionDetail,
    };
  },
  methods: {
    formatDate(date) {
      return this.$dayjs(Number(date)).format('DD/MM/YYYY');
    },
  },
  created() {
    console.log(this.$route.params.transactionDetail);
  }
}
</script>

<style scoped>

</style>

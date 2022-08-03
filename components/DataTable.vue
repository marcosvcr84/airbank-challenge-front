<template>
  <div class="flex flex-col">
    <div>
      <h1>Transactions</h1>
    </div>
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
              <label>Account</label>
              <select v-model="selectedAccount"
                      @change="selectAccount($event)"
                      style="height: 35px;
                      margin-top: 1%;
                      background-color: white;
                      text-align: center">
                <option v-for="item in allAccounts" v-bind:value="item.id">
                  {{ item.name }}
                </option>
              </select>
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
              <label>Starting date</label>
              <div>
                <input v-model="startingDate"
                       style="height: 35px;
                       margin-top: 1%;
                       background-color: white;
                       text-align: center"
                       v-maska="'####-##-##'"
                       placeholder="YYYY-MM-DD">
              </div>
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
              <label>End date</label>
              <div>
                <input v-model="endDate"
                       style="height: 35px;
                       margin-top: 1%;
                       background-color: white;
                       text-align: center"
                       v-maska="'####-##-##'"
                       placeholder="YYYY-MM-DD">
              </div>
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
              <div>
                <button class="col-auto" style="background-color: darkgray; color: white; height: 35px; width: 100px; text-align: center" @click="getFilteredTransactions(1)">Filter</button>&nbsp&nbsp&nbsp
              </div>
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
              <div>
                <button class="col-auto" style="background-color: darkgray; color: white; height: 35px; width: 100px; text-align: center" @click="cleanFilter">Clean Filter</button>&nbsp&nbsp&nbsp
              </div>
            </th>
          </tr>
          </thead>
        </table>
      </div>
    </div>

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
            <tr v-for="transaction of allTransactions" @click="openDetail(transaction)" style="cursor: pointer">
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
      <div class="px-6 py-4">
        <button class="col-auto" style="background-color: darkgray; color: white; height: 35px; width: 100px; text-align: center" @click="prevPage">Previous</button>&nbsp&nbsp&nbsp
        <button class="col-auto" style="background-color: darkgray; color: white; height: 35px; width: 100px; text-align: center" @click="nextPage">Next</button>
      </div>
    </div>
</template>

<script lang="ts">
import getAllTransactionsGql from '~/apollo/queries/getAllTransactions.gql';
import getCategoryGql from '~/apollo/queries/getCategory.gql';
import getAccountGql from '~/apollo/queries/getAccount.gql';
import getAllAccountsGql from '~/apollo/queries/getAllAccounts.gql';
import getTransByAccountIdAndRangeDateGql from '~/apollo/queries/getTransByAccountIdAndRangeDate.gql';
import { maska } from 'maska';

export default {
  directives: { maska },
  name: 'DataTable',
  data() {
    return {
      allTransactions: [],
      allAccounts: [],
      filteredTransactions: false,
      selectedAccount: "",
      selectedAccountId: "",
      startingDate: "",
      endDate: "",
      pageSize: 1,
      currentPage:1
    };
  },
  methods: {
    formatDate(date: string) {
      return this.$dayjs(Number(date)).format('DD/MM/YYYY');
    },
    nextPage:async function () {
      if (this.allTransactions.length) this.currentPage++;
      if (!this.filteredTransactions) {
        await this.getAllTransactions(this.currentPage);
      } else {
        await this.getFilteredTransactions(this.currentPage);
      }
    },
    prevPage:async function () {
      if (this.currentPage > 1) this.currentPage--;
      if (!this.filteredTransactions) {
        await this.getAllTransactions(this.currentPage);
      } else {
        await this.getFilteredTransactions(this.currentPage);
      }
    },
    selectAccount: function(account: any) {
      this.selectedAccountId = account.target.value;
    },
    cleanFilter: async function() {
      this.filteredTransactions = false;
      this.getAllTransactions(1);
    },
    openDetail: function(transaction: any) {
      this.$router.push({
        name: "TransactionDetail",
        params: {
          transactionDetail: transaction
        }
      })
    },
    getFilteredTransactions: async function(page: number) {
      this.filteredTransactions = true;
      const result = await this.$apollo.query({
        query: getTransByAccountIdAndRangeDateGql,
        variables: {
          page: page,
          accountId: this.selectedAccountId,
          initialDate: this.startingDate,
          finalDate: this.endDate
        }
      });
      const resultTransactions = result.data.getTransByAccountIdAndRangeDate;
      if (result.data && result.data.getTransByAccountIdAndRangeDate) {
        for (const transaction of result.data.getTransByAccountIdAndRangeDate) {
          let index = result.data.getTransByAccountIdAndRangeDate.indexOf(transaction);
          const category = (transaction.categoryId && transaction.categoryId !== '') ? await this.$apollo.query({
            query: getCategoryGql,
            variables: {
              categoryId: transaction.categoryId
            }
          }) : "";
          const account = (transaction.accountId && transaction.accountId !== '') ? await this.$apollo.query({
            query: getAccountGql,
            variables: {
              accountId: transaction.accountId
            }
          }): "";
          resultTransactions[index].categoryName = category !== "" ? category.data.getCategory.name : "";
          resultTransactions[index].categoryColor = category !== "" ? category.data.getCategory.color : "";
          resultTransactions[index].accountName = account !== "" ? account.data.getAccount.name : "";
        }
        this.allTransactions = resultTransactions;
      }
    },
    getAllAccounts: async function () {
      const result = await this.$apollo.query({
        query: getAllAccountsGql
      });
      this.allAccounts = result.data.getAllAccounts;
    },
    getAllTransactions: async function (page: number) {
      const result = await this.$apollo.query({
        query: getAllTransactionsGql,
        variables: {
          page: page
        }
      });
      const resultTransactions = result.data.getAllTransactions;
      if (result.data && result.data.getAllTransactions) {
        for (const transaction of result.data.getAllTransactions) {
          let index = result.data.getAllTransactions.indexOf(transaction);
          const category = (transaction.categoryId && transaction.categoryId !== '') ? await this.$apollo.query({
            query: getCategoryGql,
            variables: {
              categoryId: transaction.categoryId
            }
          }) : "";
          const account = (transaction.accountId && transaction.accountId !== '') ? await this.$apollo.query({
            query: getAccountGql,
            variables: {
              accountId: transaction.accountId
            }
          }): "";
          resultTransactions[index].categoryName = category !== "" ? category.data.getCategory.name : "";
          resultTransactions[index].categoryColor = category !== "" ? category.data.getCategory.color : "";
          resultTransactions[index].accountName = account !== "" ? account.data.getAccount.name : "";
        }
        this.allTransactions = resultTransactions;
      }
    }
  },
  async created() {
    await this.getAllTransactions(1);
    await this.getAllAccounts();
  }
}
</script>


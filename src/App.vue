<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpense :income="+income" :expenses="+expenses" />
    <TransationList :transactions="transactions" @transactionDeleted="handelTransactiononDeleted" />
    <AddTracsaction @transactiononSubmitted="handelTransactiononSubmitted" />
  </div>
</template>

<script setup>
//  <!-- دالة الاعداد داخل تاغ js طريقة احدث ولا تحتاج الى export -->
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpense from './components/IncomeExpense.vue'
import TransationList from './components/TransationList.vue'
import AddTracsaction from './components/AddTracsaction.vue'
import { useToast } from 'vue-toastification'
import { ref, computed, onMounted } from 'vue'

const toast = useToast()

const transactions = ref([])

onMounted(() => {
  const savedTransaction = JSON.parse(localStorage.getItem('transactions'))
  if (savedTransaction) {
    transactions.value = savedTransaction
  }
})

// get total
const total = computed(() => {
  return transactions.value.reduce((ass, transactions) => {
    return ass + transactions.amount
  }, 0)
})

// get income
const income = computed(() => {
  return transactions.value
    .filter((transactions) => transactions.amount > 0)
    .reduce((ass, transactions) => {
      return ass + transactions.amount
    }, 0)
    .toFixed(2)
})

// get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transactions) => transactions.amount < 0)
    .reduce((ass, transactions) => {
      return ass + transactions.amount
    }, 0)
    .toFixed(2)
})

// add transactions

const handelTransactiononSubmitted = (transactionData) => {
  transactions.value.push({
    id: generteUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  })
  savedTransactionToLoclStorage()
  toast.success('transaction added')
}

// انشاء معرف فريد
const generteUniqueId = () => {
  return Math.floor(Math.random() * 1000000)
}

// delet transaction
const handelTransactiononDeleted = (id) => {
  transactions.value = transactions.value.filter((transactions) => transactions.id !== id)
  savedTransactionToLoclStorage()
  toast.success('transaction deleted')
}

//save to localstorge
const savedTransactionToLoclStorage = () => {
  localStorage.setItem('transaction', JSON.stringify(transactions.value))
}
</script>



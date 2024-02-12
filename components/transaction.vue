<template>
  <div
    class="grid grid-cols-3 py-4 border-b border-gray-200 dark:border-gray-800 text-gray-900 dark:text-gray-100"
  >
    <div class="flex items-center justify-between space-x-4 col-span-2">
      <div class="flex items-center space-x-1">
        <UIcon :name="icon" :class="[iconColor]" />
        <div>{{ transaction.description }}</div>
      </div>
      <div>
        <UBadge v-if="transaction.category" color="white">{{
          transaction.category
        }}</UBadge>
      </div>
    </div>
    <div class="flex items-center justify-end space-x-2">
      <div>{{ formattedAmount }}</div>
      <div>
        <ClientOnly>
          <UDropdown :items="items" :popper="{ placement: 'bottom-start' }">
            <UButton
              color="white"
              variant="ghost"
              trailing-icon="i-heroicons-ellipsis-horizontal"
              :loading="isLoading"
            />
            <TransactionModal v-model="isOpen" :transaction="transaction" @saved="emit('edited')" />
          </UDropdown>
        </ClientOnly>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  transaction: Object,
});

const isOpen = ref(false)
const emit = defineEmits(['deleted', 'edited'])

const inIncome = computed(() => props.transaction.type === "Income");
const icon = computed(() =>
  inIncome.value ? "i-heroicons-arrow-up-right" : "i-heroicons-arrow-down-left"
);
const iconColor = computed(() =>
  inIncome.value ? "text-green-600" : "text-red-600"
);
const { formattedAmount } = useCurrency(props.transaction.amount);

const isLoading = ref(false)
const { toastSuccess, toastError } = useAppToast()
const supabase = useSupabaseClient();

const deleteTransaction = async () => {
  isLoading.value = true

  try {
    await supabase
      .from('transactions')
      .delete()
      .eq('id', props.transaction.id)
    
      toastSuccess({
      title: 'Transaction Deleted',
    })
    emit('deleted', props.transaction.id)
  } catch (error) {
    toastError({
      title: 'Something gone wrong',
    })
  } finally {
    isLoading.value = false
  }
}

const items = [
  [
    {
      label: "Edit",
      icon: "i-heroicons-pencil-square-20-solid",
      click: () => isOpen.value = true
    },
    {
      label: "Delete",
      icon: "i-heroicons-trash-20-solid",
      click: deleteTransaction,
    },
  ],
];
</script>

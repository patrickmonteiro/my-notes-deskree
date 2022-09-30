<template>
  <q-page padding>
    <q-form @submit="handleSubmit">
      <q-input
        label="Título"
        v-model="form.title"
        outlined
      />

      <q-editor
        v-model="form.description"
        min-height="5rem"
        class="q-mt-sm"
        toolbar-rounded
      />

      <div class="row justify-center">
        <q-btn
          v-if="isUpdate"
          label="Deletar"
          color="negative"
          class="q-mt-md q-mr-md"
          rounded
          @click="handleDeleteNote"
        />
        <q-btn
          label="Voltar"
          color="white"
          text-color="black"
          class="q-mt-md q-mr-md"
          :to="{ name: 'home' }"
          rounded
        />
        <q-btn
          label="Cadastrar"
          color="primary"
          class="q-mt-md"
          rounded
          type="submit"
        />
      </div>
    </q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted, computed } from 'vue'
import { api } from 'boot/axios'
import { useRouter, useRoute } from 'vue-router'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'FormTeams',
  setup () {
    const form = ref({
      title: '',
      description: ''
    })
    const router = useRouter()
    const route = useRoute()
    const isUpdate = computed(() => route.params.uid)
    const q = useQuasar()

    onMounted(() => {
      if (isUpdate.value) {
        handleGetNote(route.params.uid)
      }
    })

    const handleSubmit = async () => {
      try {
        if (isUpdate.value) {
          await api.patch(`notes/${route.params.uid}`, form.value)
        } else {
          await api.post('notes', form.value)
        }
        router.push({ name: 'home' })
      } catch (error) {
        console.error(error)
      }
    }

    const handleGetNote = async (uid) => {
      try {
        const { data } = await api.get(`notes/${uid}`)
        form.value = data.data
      } catch (error) {
        console.error(error)
      }
    }

    const handleDeleteNote = async () => {
      try {
        await api.delete(`notes/${route.params.uid}`)
        q.notify({
          type: 'positive',
          message: 'Nota excluída com sucesso!'
        })
        router.push({ name: 'home' })
      } catch (error) {
        console.error(error)
      }
    }

    return {
      form,
      handleSubmit,
      handleDeleteNote,
      isUpdate
    }
  }
})
</script>

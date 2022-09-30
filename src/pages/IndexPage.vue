<template>
  <q-page padding>
    <div class="row">
      <q-list class="col-12" bordered separator>
        <q-item
          v-for="note in notes"
          :key="note.uid"
          clickable
          v-ripple:primary
          @click="handleEditNote(note.uid)"
        >
          <q-item-section>
            <q-item-label>{{ note.attributes.title }}</q-item-label>
            <q-item-label caption>
              <span v-html="limitText(note.attributes.description)"></span>
            </q-item-label>
          </q-item-section>
        </q-item>
      </q-list>
    </div>

    <q-page-sticky position="bottom-right" :offset="[18, 18]">
      <q-btn fab icon="add" color="primary" label="Novo" :to="{ name: 'form' }" />
    </q-page-sticky>

  </q-page>
</template>

<script>
import { defineComponent, onMounted, ref } from 'vue'
import { api } from 'boot/axios'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const notes = ref([])
    const router = useRouter()

    onMounted(() => {
      handleGetNotes()
    })

    const handleGetNotes = async () => {
      try {
        const { data } = await api.get('notes')
        notes.value = data.data
      } catch (error) {
        console.error(error)
      }
    }

    const handleEditNote = (uid) => {
      router.push({ name: 'form', params: { uid } })
    }

    const limitText = (text) => {
      return text.substring(0, 80) + '...'
    }

    return {
      notes,
      handleEditNote,
      limitText
    }
  }
})
</script>

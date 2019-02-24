<template>
    <div>
    <q-btn fab color="warning" @click="showNotifications = true" :disabled="!notifications.length" >
      <q-icon name="far fa-bell"/>
    </q-btn>
    <q-dialog v-model="showNotifications">
      <q-card>
        <q-card-section>
          <div class="text-h6">Notifications</div>
        </q-card-section>
        <q-card-section>
          <q-list bordered separator>
            <q-item v-for="notif in notifications" :key="notif.id" clickable v-ripple>
              <q-item-section>
                <q-item-label>
                  {{ notif.message }}
                </q-item-label>
              </q-item-section>
            </q-item>
          </q-list>
        </q-card-section>
      </q-card>
    </q-dialog>
  </div>
</template>

<script>
const MOCK_NOTIFS = [
  { message: 'this is a notification', id: 1 },
  { message: 'this is also notification', id: 2 }
]

export default {
  name: 'Notifications',
  data: function () {
    return {
      showNotifications: false,
      notifications: MOCK_NOTIFS
    }
  }
}
</script>

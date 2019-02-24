<template>
    <div>
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
export default {
  name: 'NotifDialog',
  props: ['showNotifications', 'notifications'],
  data: function () {
    return {
    }
  }
}
</script>

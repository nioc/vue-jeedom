<template>
  <b-collapse class="card mb-4" animation="slide" aria-id="databaseContent" :open="false" @open="getDatabaseCollections">
    <header slot="trigger" slot-scope="props" class="card-header" role="button" aria-controls="databaseContent">
      <p class="card-header-title">
        <span class="icon"><i class="fa fa-database" /></span><span>Base de données</span>
      </p>
      <a class="card-header-icon">
        <i class="fa" :class="props.open ? 'fa-caret-down' : 'fa-caret-up'" />
      </a>
    </header>

    <section class="card-content">
      <b-loading v-model="isLoading" :is-full-page="false" />

      <div class="field">
        <div class="control">
          <label class="label">Collections à inclure dans la sauvegarde (toutes par défaut)</label>
          <b-table :data="collections" striped hoverable checkable :header-checkable="false" :checked-rows.sync="selectedCollections" :mobile-cards="false" sort-icon="menu-up" default-sort="name">
            <b-table-column v-slot="props" field="name" label="Nom" sortable>
              <span class="is-family-code">{{ props.row.name }}</span>
            </b-table-column>
            <b-table-column v-slot="props" field="stats.count" label="Nombre" sortable numeric>
              {{ props.row.stats.count }}
            </b-table-column>
            <b-table-column v-slot="props" field="stats.size" label="Taille" sortable numeric>
              {{ getHumanSizeCei(props.row.stats.size) }}
            </b-table-column>
            <b-table-column v-slot="props" field="stats.avgObjSize" label="Taille unitaire" sortable numeric>
              {{ getHumanSizeCei(props.row.stats.avgObjSize) }}
            </b-table-column>
            <b-table-column v-slot="props" field="stats.storageSize" label="Données" sortable numeric>
              {{ getHumanSizeCei(props.row.stats.storageSize) }}
            </b-table-column>
            <b-table-column v-slot="props" field="stats.totalIndexSize" label="Index" sortable numeric>
              {{ getHumanSizeCei(props.row.stats.totalIndexSize) }}
            </b-table-column>
          </b-table>
        </div>
      </div>

      <div class="field">
        <div class="control">
          <label class="label">Format</label>
          <b-switch v-model="isJson" type="is-danger" passive-type="is-primary">{{ isJson ? 'JSON (incompatible avec l\'import)' : 'BSON encodé' }}</b-switch>
        </div>
      </div>

      <span class="buttons">
        <button class="button is-primary" title="Sauvegarder les collections sélectionnées" @click="getDatabaseBackup">
          <span class="icon"><i class="fa fa-download" /></span>
          <span>Sauvegarder</span>
        </button>
      </span>

      <span class="buttons">
        <div class="file has-name is-warning" title="Charger une sauvegarde">
          <label class="file-label">
            <input ref="file" class="file-input" type="file" @input="importDatabaseBackup">
            <span class="file-cta is-warning">
              <span class="file-icon"><i class="fas fa-cloud-upload-alt" /></span>
              <span class="file-label">Importer</span>
            </span>
            <span class="file-name" :class="{'has-text-grey-light': file}">{{ file.name || 'fichier.json' }}</span>
          </label>
        </div>
      </span>
    </section>
  </b-collapse>
</template>

<script>
import { ConversionMixin } from '@/mixins/Conversion'

export default {
  name: 'SystemDatabase',
  mixins: [
    ConversionMixin,
  ],
  data () {
    return {
      isLoading: false,
      collections: [],
      selectedCollections: [],
      file: {},
      isJson: false,
    }
  },
  methods: {
    async getDatabaseCollections () {
      this.isLoading = true
      try {
        this.collections = await this.$Provider.getDatabaseCollections()
      } catch (error) {
        this.$store.commit('app/setInformation', { type: 'is-danger', message: error.message })
      }
      this.isLoading = false
    },
    async getDatabaseBackup () {
      this.isLoading = true
      try {
        await this.$Provider.getDatabaseBackup({ isJson: this.isJson, collections: this.selectedCollections.map((collection) => collection.name) })
      } catch (error) {
        this.$store.commit('app/setInformation', { type: 'is-danger', message: error.message })
      }
      this.isLoading = false
    },
    async importDatabaseBackup () {
      this.file = this.$refs.file.files[0]
      if (!this.file) {
        return
      }
      this.isLoading = true
      try {
        const results = await this.$Provider.importDatabaseBackup(this.file)
        let message = '<ul>'
        results.forEach((result) => {
          message += `<li>${result.name}: ${result.count}</li>`
        })
        message += '</ul>'
        this.$buefy.dialog.alert({
          title: 'Base de données restaurée',
          message,
          hasIcon: true,
          icon: 'database',
          iconPack: 'fa',
        })
        this.file = {}
      } catch (error) {
        this.$buefy.dialog.alert({
          type: 'is-danger',
          title: 'Erreur lors de la restauration',
          message: error.message,
          hasIcon: true,
          icon: 'database',
          iconPack: 'fa',
        })
      }
      this.isLoading = false
    },
  },
}
</script>

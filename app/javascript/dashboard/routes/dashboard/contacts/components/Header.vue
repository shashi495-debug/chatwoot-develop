<template>
  <header class="header">
    <div class="table-actions-wrap">
      <div class="left-aligned-wrap">
        <woot-sidemenu-icon />
        <h1 class="page-title header-title">
          {{ headerTitle }}
        </h1>
      </div>
      <div class="right-aligned-wrap">
        <div class="search-wrap">
          <div class="search-icon-container">
            <fluent-icon icon="search" class="search-icon" />
          </div>
          <input
            type="text"
            :placeholder="$t('CONTACTS_PAGE.SEARCH_INPUT_PLACEHOLDER')"
            class="contact-search"
            :value="searchQuery"
            @keyup.enter="submitSearch"
            @input="inputSearch"
          />
          <woot-button
            :is-loading="false"
            class="clear"
            :class-names="searchButtonClass"
            @click="submitSearch"
          >
            {{ $t('CONTACTS_PAGE.SEARCH_BUTTON') }}
          </woot-button>
        </div>
        <div v-if="hasActiveSegments">
          <woot-button
            class="margin-right-1 clear"
            color-scheme="secondary"
            icon="edit"
            @click="onToggleEditSegmentsModal"
          >
            {{ $t('CONTACTS_PAGE.FILTER_CONTACTS_EDIT') }}
          </woot-button>
          <woot-button
            class="margin-right-1 clear"
            color-scheme="alert"
            icon="delete"
            @click="onToggleDeleteSegmentsModal"
          >
            {{ $t('CONTACTS_PAGE.FILTER_CONTACTS_DELETE') }}
          </woot-button>
        </div>
        <div v-if="!hasActiveSegments" class="filters__button-wrap">
          <div v-if="hasAppliedFilters" class="filters__applied-indicator" />
          <woot-button
            class="margin-right-1 clear"
            color-scheme="secondary"
            data-testid="create-new-contact"
            icon="filter"
            @click="toggleFilter"
          >
            {{ $t('CONTACTS_PAGE.FILTER_CONTACTS') }}
          </woot-button>
        </div>

        <woot-button
          v-if="hasAppliedFilters && !hasActiveSegments"
          class="margin-right-1 clear"
          color-scheme="alert"
          variant="clear"
          icon="save"
          @click="onToggleSegmentsModal"
        >
          {{ $t('CONTACTS_PAGE.FILTER_CONTACTS_SAVE') }}
        </woot-button>
        <woot-button
          class="margin-right-1 clear"
          color-scheme="success"
          icon="person-add"
          data-testid="create-new-contact"
          @click="toggleCreate"
        >
          {{ $t('CREATE_CONTACT.BUTTON_LABEL') }}
        </woot-button>

        <woot-button
          v-if="isAdmin"
          color-scheme="info"
          icon="upload"
          class="clear"
          @click="toggleImport"
        >
          {{ $t('IMPORT_CONTACTS.BUTTON_LABEL') }}
        </woot-button>

        <woot-button
          v-if="isAdmin"
          color-scheme="info"
          icon="download"
          class="clear"
          @click="submitExport"
        >
          {{ $t('EXPORT_CONTACTS.BUTTON_LABEL') }}
        </woot-button>
      </div>
    </div>
  </header>
</template>

<script>
import { mapGetters } from 'vuex';
import adminMixin from 'dashboard/mixins/isAdmin';

export default {
  mixins: [adminMixin],
  props: {
    headerTitle: {
      type: String,
      default: '',
    },
    searchQuery: {
      type: String,
      default: '',
    },
    segmentsId: {
      type: [String, Number],
      default: 0,
    },
  },
  data() {
    return {
      showCreateModal: false,
      showImportModal: false,
    };
  },
  computed: {
    searchButtonClass() {
      return this.searchQuery !== '' ? 'show' : '';
    },
    ...mapGetters({
      getAppliedContactFilters: 'contacts/getAppliedContactFilters',
    }),
    hasAppliedFilters() {
      return this.getAppliedContactFilters.length;
    },
    hasActiveSegments() {
      return this.segmentsId !== 0;
    },
  },
  methods: {
    onToggleSegmentsModal() {
      this.$emit('on-toggle-save-filter');
    },
    onToggleEditSegmentsModal() {
      this.$emit('on-toggle-edit-filter');
    },
    onToggleDeleteSegmentsModal() {
      this.$emit('on-toggle-delete-filter');
    },
    toggleCreate() {
      this.$emit('on-toggle-create');
    },
    toggleFilter() {
      this.$emit('on-toggle-filter');
    },
    toggleImport() {
      this.$emit('on-toggle-import');
    },
    submitExport() {
      this.$emit('on-export-submit');
    },
    submitSearch() {
      this.$emit('on-search-submit');
    },
    inputSearch(event) {
      this.$emit('on-input-search', event);
    },
  },
};
</script>

<style lang="scss" scoped>
.page-title {
  margin: 0;
}
.table-actions-wrap {
  display: flex;
  justify-content: space-between;
  width: 100%;
  padding: var(--space-small) var(--space-normal) var(--space-small)
    var(--space-normal);
}

.left-aligned-wrap {
  display: flex;
  align-items: center;
  justify-content: center;
  max-width: 100%;
  min-width: var(--space-mega);

  .header-title {
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    margin: 0 var(--space-small);
  }
}

.right-aligned-wrap {
  display: flex;
}

.search-wrap {
  max-width: 400px;
  min-width: 150px;
  display: flex;
  align-items: center;
  position: relative;
  margin-right: var(--space-small);
  margin-left: var(--space-small);

  .search-icon-container {
    display: flex;
    align-items: center;
    position: absolute;
    height: 100%;
    left: var(--space-one);

    .search-icon {
      height: var(--font-size-medium);
      line-height: 2.25rem;
      font-size: var(--font-size-small);
      color: var(--b-700);
    }
  }
  .contact-search {
    margin: 0;
    height: 2.375rem;
    width: 100%;
    font-size: var(--font-size-small);
    padding-left: calc(var(--space-large) + var(--space-smaller));
    padding-right: 3.75rem;
    border-color: var(--s-100);
  }

  .button {
    margin-left: var(--space-small);
    height: 2rem;
    right: var(--space-smaller);
    position: absolute;
    padding: 0 var(--space-small);
    transition: transform 100ms linear;
    opacity: 0;
    transform: translateX(-1px);
    visibility: hidden;
  }

  .button.show {
    opacity: 1;
    transform: translateX(0);
    visibility: visible;
  }
}
.filters__button-wrap {
  position: relative;

  .filters__applied-indicator {
    position: absolute;
    height: var(--space-small);
    width: var(--space-small);
    top: var(--space-smaller);
    right: var(--space-slab);
    background-color: var(--s-500);
    border-radius: var(--border-radius-rounded);
  }
}
</style>

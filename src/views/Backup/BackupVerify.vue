<template>
  <div class="backup-step h-full sm:w-120">
    <div class="backup-step-title">Verify backup</div>
    <div class="backup-step-subtitle">Please tap each word in the correct order</div>
    <div class="backup-step-content">
      <BackupPhraseItem
        v-for="(word, index) in userPhrase"
        :key="index"
        :index="index"
        :word="word"
        :border="true"
        :is-cursor="true"
        @click.native="removeWord({ index })"
      />
    </div>
    <div class="backup-step-content-item _verify">Let’s verify your backup phrase</div>
    <div class="backup-step-content">
      <BackupPhraseItem
        v-for="(word, index) in randomPhrase"
        :key="index"
        :index="index"
        :word="word"
        :is-cursor="true"
        @click.native="selectWord({ index })"
      />
    </div>
    <div class="backup-step-footer">
      <div class="backup-step-content-item">Is this correct?</div>
      <div class="backup-step-button _clear">
        <Button
          text="Clear"
          type="secondary"
          width="full"
          @click="onClear"
        />
      </div>
      <div class="backup-step-button _confirm">
        <Button
          :disabled="isIncorrectKey"
          text="Confirm"
          width="full"
          @click="!isIncorrectKey && $emit('change', stepConfig['BACKUP_FINISH'])"
        />
      </div>
    </div>
  </div>
</template>

<script>
import BackupPhraseItem from './BackupPhraseItem'
import Button from '@/components/Button/Button'

export default {
  components: {
    BackupPhraseItem,
    Button
  },
  props: {
    backupPhrase: {
      type: Array,
      default() {
        return []
      }
    },
    stepConfig: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      userPhrase: [],
      randomPhrase: []
    }
  },
  computed: {
    isIncorrectKey() {
      console.info(this.backupPhrase, this.userPhrase)
      return this.backupPhrase.join(' ') !== this.userPhrase.join(' ')
    }
  },
  mounted() {
    this.randomPhrase = this.generateRandomPhrase()
  },
  methods: {
    generateRandomPhrase() {
      return this.backupPhrase.slice(0).sort(() => {
        return Math.random() - 0.5
      })
    },
    selectWord({ index }) {
      this.userPhrase.push(this.randomPhrase[index])
      this.randomPhrase = this.randomPhrase.filter((item, ndx) => ndx !== index)
    },
    removeWord({ index }) {
      this.randomPhrase.push(this.userPhrase[index])
      this.userPhrase = this.userPhrase.filter((item, ndx) => ndx !== index)
    },
    onClear() {
      this.randomPhrase = this.generateRandomPhrase()
      this.userPhrase = []
    }
  }
}
</script>
<style lang="scss" scoped>
  .backup-step {
    user-select: none;
    display: flex;
    flex-direction: column;
    text-align: center;
    font-family: config('fonts.gotham');
    padding: 0 1.25rem 1.25rem;
    min-height: 30.4375rem;

    .backup-step-title {
      margin-top: 0.9375rem;
      text-transform: uppercase;
      font-family: config('fonts.gotham-medium');
      font-size: config('textSizes.lg');
    }

    .backup-step-subtitle {
      margin: 0.9375rem auto 3rem;
      font-weight: config('fontWeights.semibold');
      font-size: config('textSizes.lg');
      text-align: center;
    }

    .backup-step-content {
      margin: auto;
      padding: 0 1.2rem;
      display: flex;
      flex-direction: row;
      flex-wrap: wrap;
      justify-content: center;
      font-weight: config('fontWeights.semibold');

      .backup-step-content-item {
        font-weight: config('fontWeights.semibold');
      }
    }

    .backup-step-footer {
      display: flex;
      flex-direction: column;
      margin-top: auto;
    }

    .backup-step-button {
      box-sizing: border-box;
      display: inline-block;
      margin-top: 1rem;

    }
    ._verify {
      margin: .5rem auto;
    }
  }
</style>

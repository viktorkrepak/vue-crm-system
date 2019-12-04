<template>
  <div class="col s12 m6">
    <div>
      <div class="page-subtitle">
        <h4>Создать</h4>
      </div>

      <form @submit.prevent="submitHandler">
        <div class="input-field">
          <input
            v-model="title"
            :class="{invalid: $v.title.$dirty && !$v.title.required}"
            id="name"
            type="text"
          />
          <label for="name">Название</label>
          <span
            class="helper-text invalid"
            v-if="$v.title.$dirty && !$v.title.required"
          >Введите название категории</span>
        </div>

        <div class="input-field">
          <input
            v-model.number="limit"
            id="limit"
            type="number"
            :class="{invalid:$v.limit.$dirty && !$v.title.minValue}"
          />
          <label for="limit">Лимит</label>
          <span class="helper-text invalid" v-if="$v.limit.$dirty && !$v.title.minValue">
            Минимальное значение
            {{$v.limit.$params.minValue.min}}
          </span>
        </div>

        <button class="btn waves-effect waves-light" type="submit">
          Создать
          <i class="material-icons right">send</i>
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import { required, minValue } from "vuelidate/lib/validators";
export default {
  data: () => ({
    title: "",
    limit: 50
  }),
  validations: {
    title: { required },
    limit: { minValue: minValue(50) }
  },
  mounted() {
    M.updateTextFields();
  },
  methods: {
    async submitHandler() {
      if (this.$v.$invalid) {
        this.$v.$touch();
        return;
      }

      try {
        const category = await this.$store.dispatch("createCategory", {
          title: this.title,
          limit: this.limit
        });
        this.title = "";
        this.limit = 50;
        this.$v.$reset();
        this.$message("Категория была создана");
        this.$emit("created", category);
      } catch (e) {}
    }
  }
};
</script>

<style scoped>
</style>

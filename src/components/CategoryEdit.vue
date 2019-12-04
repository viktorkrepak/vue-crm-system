<template>
  <div class="col s12 m6">
    <div>
      <div class="page-subtitle">
        <h4>Редактировать</h4>
      </div>

      <form @submit.prevent="submitHandler">
        <div class="input-field">
          <select ref="select" v-model="current">
            <option v-for="c of categories" :key="c.id" :value="c.id">{{c.title}}</option>
          </select>
          <label>Выберите категорию</label>
        </div>

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
            :class="{invalid:$v.limit.$dirty && !$v.limit.minValue}"
          />
          <label for="limit">Лимит</label>
          <span class="helper-text invalid" v-if="$v.limit.$dirty && !$v.limit.minValue">
            Минимальное значение
            {{$v.limit.$params.minValue.min}}
          </span>
        </div>

        <button class="btn waves-effect waves-light" type="submit">
          Обновить
          <i class="material-icons right">send</i>
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import { required, minValue } from "vuelidate/lib/validators";
export default {
  name: "CategoryEdit",
  props: {
    categories: {
      type: Array,
      required: true
    }
  },

  data: () => ({
    title: "",
    limit: 50,
    select: null,
    current: null
  }),
  watch: {
    current(catId) {
      const { title, limit } = this.categories.find(c => c.id === catId);
      (this.title = title), (this.limit = limit);
    }
  },
  validations: {
    title: { required },
    limit: { minValue: minValue(50) }
  },

  mounted() {
    M.updateTextFields();
    this.select = M.FormSelect.init(this.$refs.select);
  },
  created() {
    const { id, title, limit } = this.categories[0];
    (this.current = id), (this.title = title), (this.limit = limit);
  },
  destroyed() {
    if (this.select && this.select.destroy) {
      this.select.destroy();
    }
  },
  methods: {
    async submitHandler() {
      if (this.$v.$invalid) {
        this.$v.$touch();
        return;
      }
      try {
        const categoryData = {
          id: this.current,
          title: this.title,
          limit: this.limit
        };
        await this.$store.dispatch("updateCategory", categoryData);
        this.$message("Категория успешно обновлена");
        this.$emit("updated", categoryData);
      } catch (e) {}
    }
  }
};
</script>

<style scoped>
</style>

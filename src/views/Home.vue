<template>
  <v-layout row wrap>
    <v-flex text-xs-center>
      <!-- header -->
      <h1 class="primary--text display-2 font-weight-medium my-3">זה מה שנגה אוכלת</h1>
      <h6 class="primary--text display-1 font-weight-medium my-3">(וגם מה שלא)</h6>
      <v-card>
        <v-list class="pa-0">
          <v-list-tile>
            <v-text-field
              @keydown.enter="$event.target.blur()"
              autofocus
              browser-autocomplete="off"
              clearable
              color="primary"
              flat
              hide-details
              maxlength="1023"
              placeholder="מה מחפשים?"
              solo
              v-model="searchText"
            ></v-text-field>
          </v-list-tile>
        </v-list>
      </v-card>
      <!-- main -->
      <v-card class="mt-3" v-show="foods.length">
        <v-progress-linear class="my-0" v-model="progressPercentage"/>
        <v-card-actions class="px-3" v-show="foods.length">
          <span class="primary--text">
            לנגה מותר לאכול {{ remaining }} מתוך {{ foods.length }} אוכלים!
          </span>
          <v-spacer></v-spacer>
        </v-card-actions>
        <v-list class="pa-0">
          <template v-for="food in filteredFoods">
            <v-divider :key="`${food.uid}-divider`"></v-divider>
            <TodoItem
              :key="food.uid"
              :food="food"
            />
          </template>
        </v-list>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import { mapActions } from 'vuex'
import TodoItem from '@/components/TodoItem.vue'
import FooterInfo from '@/components/FooterInfo.vue'

const filters = {
  all: foods => foods,
  good: foods => foods.filter(food => !food.isOkay),
  bad: foods => foods.filter(food => food.isOkay)
}
const search = (foods, text) => foods.filter(food => food.text.includes(text))
export default {
  props: ['filter'],
  components: {
    TodoItem,
    FooterInfo
  },
  data () {
    return {
      searchText: '',
      filters: filters,
      visibility: this.filter
    }
  },
  computed: {
    foods () {
      return this.$store.state.foods
    },
    allChecked () {
      return this.foods.every(todo => todo.done)
    },
    filteredFoods () {
      return filters[this.visibility](search(this.foods, this.searchText))
    },
    remaining () {
      return this.foods.filter(food => food.isOkay).length
    },
    progressPercentage () {
      const len = this.foods.length
      return ((len - this.remaining) * 100) / len
    }
  },
  methods: {
    ...mapActions([
      'toggleAll',
      'clearCompleted'
    ]),
    addFood (text) {
      if (text) {
        this.$store.dispatch('addFood', text)
      }
      this.newTodo = ''
    }
  },
  filters: {
    pluralize: (n, w) => n === 1 ? w : (w + 's'),
    capitalize: s => s.charAt(0).toUpperCase() + s.slice(1)
  },
  beforeMount () {
    const notOkay = `חמאת בוטנים, כוסמת, חלב קוקוס, שמן חמנייה, פקאן, חרדל, שמיר, סובין, שומר, שמן בוטנים, דבש, גמבה, חלב סויה, קולורבי, שום, אגס, גינגר, פופקורן, שעורה, סירופ מייפל, עדשים ירוקות, פטריות שיטאקי, קטשופ, טראגון, שעועית לבנה, תמרים, ערמוני מים, שעועית שחורה, אנדיב, אורגנו, מלון, רום, שעועית אזוקי, שקדים, ברנדי, פפאיה, סלק, לימון, אפרסק, קולה, גויאבה, פולי סויה, סלרי, אנונה, חלב אורז, גזר, סילאן, תירס, רוטב סויה, אפרסמון, תה, שמן סויה, ברוקולי, פירות יער, שמן תירס, אספרגוס, מלפפון, בצל, טפיוקה, חציל, בננה, לבבות דקל, פלפל ירוק, קשיו, קרמבולה, קפה, כרוב, בצל שאלוט, אגוז ברזיל, פסיפלורה, תאנים, ערמונים, שעועית לימה, משמשים`
    const okay = `צימוקים, עדשים חומות, צנוברים, חומץ, זיתים, עמילן, בטטה, כוסמין, קמח כוסמין, עדשים אדומות, אורז חום, אורז בר, אורז אדום, נבטים, גלוטן, שמן קוקוס, שסק, סטיביה, בזיליקום, מיונז, אגוזי מקדמיה, שיבולת שועל, שעועית אדומה, אצות, דובדבנים, תימין, קיווי, אורז לבן, חומץ בן יין, יין לבן, שמן זית, זרעי חרדל, גרעיני חמנייה, שמן אבוקדו, אבוקדו, סודה לשתיה, קייל, אגוז מלך, סלק, חסה, נקטרינה, שומשום, חיטה, קמח חיטה, קמח טף, קקאו, עגבניות, ארטישוק, קארי, פטריות, סוכר קנים, כרובית, פטרוזיליה, זעפרן, למון גראס, בוטנים, חומוס, טופו, טחינה, וויסקי, עמילן תפוח אדמה, תפוח אדמה, תפוח, תותים, דלעת, סוכר חום, שמן קנולה, אגוז לוז, אגוז מוסקט, צנון, אבטיח, שעועית ירוקה, קינמון, חלב שקדים, פיסטוק, אורז בסמטי, שמן שקדים, זרעי פשתן, רימונים, שמן ענבים, שזיף, סוכר דמררה, זוקיני, עלי דפנה, הל, מנגו, שעועית מונבטת, שוקולד, אבקת צ׳ילי, צ׳ילי מתוק, חומץ אורז, קפה עם קפאין, קולה דיאט, לפת, ליצ׳י, אבקת אפיה, פפריקה, וניל, בירה, פלפל שחור, שמן שומשום, שמרים, שמרי בירה, חיטה מלאה, דוחן, אננס, אורז יסמין, יין אדום, שזיף לא מיובש, קוקוס, זרעי כוסברה, רוזמרין, אשכולית, ענבים ירוקים, ענבים אדומים, צבר, כמון, קלמנטינה, שעועית מש, אפונה, מלח, זרעי צ׳יה, שמיר, חומץ תפוחים, וודקה, תרד, כרישה, תפוז, קינואה, כורכום, אבוקדו`

    okay.split(', ').forEach(food => this.$store.dispatch('addFood', { text: food, isOkay: true }))
    notOkay.split(', ').forEach(food => this.$store.dispatch('addFood', { text: food, isOkay: false }))
  }
}
</script>

<style lang="stylus">
h6
  opacity: 0.3
.v-text-field .v-input__slot
  padding: 0 !important
.subheader
  font-size: 80px !important
</style>

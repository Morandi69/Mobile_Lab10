<p align = "center">МИНИСТЕРСТВО НАУКИ И ВЫСШЕГО ОБРАЗОВАНИЯ
РОССИЙСКОЙ ФЕДЕРАЦИИ
ФЕДЕРАЛЬНОЕ ГОСУДАРСТВЕННОЕ БЮДЖЕТНОЕ
ОБРАЗОВАТЕЛЬНОЕ УЧРЕЖДЕНИЕ ВЫСШЕГО ОБРАЗОВАНИЯ
«САХАЛИНСКИЙ ГОСУДАРСТВЕННЫЙ УНИВЕРСИТЕТ»</p>
<br><br><br><br><br><br>
<p align = "center">Институт естественных наук и техносферной безопасности<br>Кафедра информатики<br>Чагочкин Никита</p>
<br><br><br>
<p align = "center">Лабораторная работа №10<br>«Базы данных и Room Library, навигация по фрагментам».<br>01.03.02 Прикладная математика и информатика</p>
<br><br><br><br><br><br><br><br><br><br><br><br>
<p align = "right">Научный руководитель<br>
Соболев Евгений Игоревич</p>
<br><br><br>
<p align = "center">г. Южно-Сахалинск<br>2023 г.</p>

***
## <p align = "center">За основу я взял код из 12 темы, и готовую базу данных из 11(БД после установки требуется загрузить на устройство)</p>
# <p align = "center">Ошибка доступа к схеме</p>
Если вы посмотрите журналы сборки, вы увидите предупреждение о том, что ваше приложение не предоставляет каталог для экспорта схемы:
warning: Schema export directory is not provided to the annotation processor so we cannot export the schema. You can either provide `room.schemaLocation` annotation processor argument OR set exportSchema to false.
 Схема базы данных — это структура базы данных, а именно: какие таблицы в базе данных, какие столбцы в этих таблицах, а также любые ограничения и отношения между этими таблицами. Room поддерживает экспорт схемы базы данных в файл, чтобы ее можно было хранить в системе управления версиями. Экспорт схемы часто бывает полезен, чтобы иметь разные версии вашей базы данных. 
Предупреждение означает, что вы не указали местоположение файла, где Room мог бы сохранить схему базы данных. Вы можете указать местоположение схемы для аннотации @Database либо отключить экспорт, чтобы удалить предупреждение. Для данного упражнения можно выбрать один из этих вариантов. 
***
## <p align = "center">Решение</p>
### Для решения данной проблемы, я отключил экспорт схемы базы данных в файле CrimeDatabase.kt

        @Database(entities = [ Crime::class ], version=1,exportSchema = false)

# <p align = "center">Эффективная перезагрузка RecyclerView</p>
Сейчас, когда пользователь возвращается на экран списка после редактирования преступления, CrimeListFragment заново выводит все видимые преступления в RecyclerView. Это крайне неэффективно, так как в большинстве случаев изменяется всего одно преступление. Обновите реализацию RecyclerView в CrimeListFragment, чтобы заново выводилась только строка, связанная с измененным преступлением. 
***
## <p align = "center">Решение</p>
### Для этого я обновил CrimeAdapter до androidx.recyclingerview.widget. ListAdapter  в файле CrimeListFragment.kt

        private inner class CrimeAdapter(var crimes: List<Crime>)
                : ListAdapter<Crime, CrimeHolder>(DiffCallBack()) {

                override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): CrimeHolder {
                    val view = layoutInflater.inflate(R.layout.list_item_crime, parent, false)
                    return CrimeHolder(view)
                }

                override fun onBindViewHolder(holder: CrimeHolder, position: Int) {
                    val crime = getItem(position)
                    holder.bind(crime)
                }
            }

### Добавил реализацию DiffUtil.itemCallback

        private inner class DiffCallBack : DiffUtil.ItemCallback<Crime>() {

                override fun areContentsTheSame(oldItem: Crime, newItem: Crime): Boolean {
                    return oldItem == newItem
                }

                override fun areItemsTheSame(oldItem: Crime, newItem: Crime): Boolean {
                    return oldItem.id == newItem.id
                }
            }

### И вместо создания нового адаптера каждый раз, я вызываю метод .submitList(crimes)

        override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
                super.onViewCreated(view, savedInstanceState)
                crimeListViewModel.crimeListLiveData.observe(
                    viewLifecycleOwner,
                    Observer { crimes ->
                        crimes?.let {
                            Log.i(TAG, "Got crimes ${crimes.size}")
                            adapter.submitList(crimes)
                        }
                    })
            }
Утилита ldd входит в состав систем Linux и BSD. В системах macOS аналогичную функциональность предлагает утилита otool с флагом -L: otool -L filename. В Windows для вывода списка зависимых библиотек служит утилита dumpbin, входящая в состав Visual Studio: dumpbin /dependents filenam

Утилиту otool проще всего описать как аналог objdump для macOS, она полезна для разбора файлов в формате Mach-O. В листинге ниже показано, как otool отображает зависимости двоичного файла в формате Mach-O от динамических библиотек, т. е. выполняет функцию ldd
Утилиту otool можно использовать для отображения информации из заголовков файла и таблицы символов, а также для дизассемблирования секции кода. Дополнительные сведения см. в странице руководства
# Tema 10: Fechas y horas en Java

??? abstract "Duración y criterios de evaluación"

    Duración estimada: 6 sesiones

    <hr />

    **Resultados de aprendizaje**

    1. Conocer las clases de manejo de fechas y horas de Java.
    2. Distinguir las clases obsoletas (`Date`, `Calendar`) del paquete moderno `java.time`.
    3. Construir, transformar, comparar y formatear fechas y horas.

    **Criterios de evaluación**

    1. Se crean fechas y horas con `now()`, `of()` y `with()` (y ajustadores temporales).
    2. Se extrae información (año, mes, día, hora...) con los métodos `get`.
    3. Se suman y restan intervalos con `plus*`/`minus*` y se comparan con `isBefore`/`isAfter`.
    4. Se obtienen fechas desde texto con `parse()` y se muestran con `format()`.
    5. Se calcula el tiempo transcurrido entre dos instantes con `Period`, `Duration` y `CronoUnit`.

## 10.1 Introducción

Java dispone de **clases específicas para manejar datos de fechas y horas** de forma correcta. Con ellas será más sencillo:

- Crear fechas y horas.
- Convertirlas.
- Realizar cálculos con ellas.
- "Parsearlas" (de texto a fecha).
- Mostrarlas con un formato determinado.

## 10.2 Clases obsoletas

Dos clases típicas para trabajar con fechas han sido:

- `java.util.Date`
- `java.util.Calendar`

Hoy en día están **obsoletas** (*deprecated*). Después apareció `java.sql.Date`, pero realmente es una **subclase de `Date`** y tampoco se usa actualmente.

!!! danger "No las uses en código nuevo"
    Tienen problemas graves (mutables, años empiezan en 1900, meses en 0, formato por defecto feo, zona horaria rara...). Si ves `SimpleDateFormat`, `Calendar.getInstance()` o `new Date()`, sabes que es código antiguo.

## 10.3 El nuevo paquete `java.time`

Con **Java 8** (2014) se crea el paquete **`java.time`** con las clases oficiales. Soluciona bastantes problemas de las clases tradicionales:

- Incluye **soporte automático** para años bisiestos.
- Zonas horarias y **cambio automático** de hora.
- Como bonus, modela el **tiempo con inmutabilidad** (operaciones devuelven copias), como vimos en POO avanzada.

### Clases más importantes

| Clase | Para qué sirve |
|-------|----------------|
| `java.time.LocalDate` | Representa **fechas** (día, mes, año) y facilita su manejo: declararlas, compararlas, sumarlas... |
| `java.time.LocalTime` | Igual que la anterior, pero para **horas**. |
| `java.time.LocalDateTime` | Combinación de las dos anteriores. |
| `java.time.ZonedDateTime` | Como `LocalDateTime`, pero teniendo en cuenta la **zona horaria**. |
| `java.time.Instant` | Parecida a `LocalDateTime`, pero almacena **nanosegundos desde el epoch UNIX** (01/01/1970). |
| `java.time.Period` | Permite obtener la **diferencia entre fechas**. |
| `java.time.Duration` | Permite obtener la **diferencia entre instantes de tiempo** (horas/minutos/segundos). |

## 10.4 Construir fechas y horas

Estas clases **carecen de constructores públicos**: se instancian usando **métodos de tipo factoría** que construyen los objetos a partir de parámetros.

Todas disponen de 3 métodos importantes:

| Método | Qué hace |
|--------|----------|
| `now()` | Crea instancias a partir de la fecha y hora **actual**. |
| `of()` | Construye fechas y horas **a partir de sus partes**. |
| `with()` | **Modifica** el objeto (devuelve una copia con el cambio). |

### 10.4.1 Fecha y hora actuales con `now()`

```java
import java.time.*;

public class Fecha01now {
    public static void main(String[] args) {
        // Creación de distintos objetos con now()
        System.out.println(LocalDate.now());       // 2026-09-11
        System.out.println(LocalTime.now());       // 21:43:56.123456
        System.out.println(LocalDateTime.now());   // 2026-09-11T21:43:56.123456
    }
}
```

### 10.4.2 Fechas específicas con `of()`

```java
import java.time.*;

public class Fecha02of {
    public static void main(String[] args) {
        // Creación de fechas con of()
        LocalDate fecha = LocalDate.of(1983, Month.MARCH, 24);
        System.out.println(fecha);                       // 1983-03-24

        LocalTime hora = LocalTime.of(20, 30, 15);
        System.out.println(hora);                        // 20:30:15

        LocalDateTime fechaConHora = LocalDateTime.of(1983, 3, 24, 20, 30, 15);
        System.out.println(fechaConHora);                // 1983-03-24T20:30:15
    }
}
```

### 10.4.3 Copias modificadas con `with()` y ajustadores temporales

```java
import java.time.*;
import java.time.temporal.*;

public class Fecha03with {
    public static void main(String[] args) {
        LocalDate ahora = LocalDate.now(); // 2026-09-11

        // with() modifica una parte de la fecha
        System.out.println(ahora.withDayOfMonth(1));  // 2026-09-01 (primer día del mes)
        System.out.println(ahora.withMonth(6));       // 2026-06-11
        System.out.println(ahora.withYear(2000));     // 2000-09-11

        // Ajustadores temporales (java.time.temporal.TemporalAdjusters)
        System.out.println(ahora.with(TemporalAdjusters.firstDayOfMonth()));   // 2026-09-01
        System.out.println(ahora.with(TemporalAdjusters.firstDayOfYear()));    // 2026-01-01
        // Próximo viernes a partir de hoy (útil para el ejercicio del viernes 13)
        System.out.println(ahora.with(TemporalAdjusters.nextOrSame(DayOfWeek.FRIDAY)));
    }
}
```

## 10.5 Información de fechas

Mediante los métodos `get` de las fechas podemos extraer mucha información:

```java
// Fecha 14/11/2022 a las 13:52:13 horas
LocalDateTime fecha = LocalDateTime.of(2022, 11, 14, 13, 52, 13);

System.out.println(fecha.getYear());   // 2022
System.out.println(fecha.getMonth());  // NOVEMBER
System.out.println(fecha.getMonthValue()); // 11
System.out.println(fecha.getDayOfMonth()); // 14
System.out.println(fecha.getDayOfWeek());  // MONDAY
System.out.println(fecha.getDayOfYear());  // 318
System.out.println(fecha.getHour());   // 13
System.out.println(fecha.getMinute()); // 52
System.out.println(fecha.getSecond()); // 13
```

## 10.6 Transformar fechas y horas

Según la clase utilizada dispondremos de métodos para **añadir o quitar intervalos**:

```java
LocalDateTime fecha = LocalDateTime.of(2022, Month.NOVEMBER, 22, 14, 30, 00);

System.out.println(fecha.plusYears(10));    // 2032-11-22T14:30
System.out.println(fecha.plusMonths(3));    // 2023-02-22T14:30
System.out.println(fecha.minusDays(5));     // 2022-11-17T14:30
System.out.println(fecha.plusHours(2));     // 2022-11-22T16:30
```

Así mismo, se pueden **comparar** con `isBefore(fecha)`, `isAfter(fecha)`:

```java
System.out.println(fecha.isBefore(LocalDateTime.of(2023, 1, 1, 0, 0))); // true
System.out.println(fecha.isAfter(LocalDateTime.of(2020, 1, 1, 0, 0)));  // true
```

!!! warning "Ojo: las clases de `java.time` son inmutables"
    Métodos como `plusDays()`, `minusDays()` o `withMonth()` **no modifican el objeto**: devuelven uno **nuevo** (como el ejemplo: el resultado hay que capturarlo o mostrarlo). El objeto original queda intacto.

## 10.7 Parsear fechas

Es posible crear fechas a partir de una **cadena de texto** mediante `parse()`. Opcionalmente admite un **segundo parámetro** para especificar el formato.

```java
// Formato por defecto (ISO): yyyy-MM-dd
LocalDate hoy = LocalDate.parse("2022-11-14");
System.out.println(hoy); // 2022-11-14

// Con un formato concreto: dd/MM/yyyy
LocalDate fecha = LocalDate.parse("24/03/1983",
        DateTimeFormatter.ofPattern("dd/MM/yyyy"));
System.out.println(fecha); // 1983-03-24
```

### Patrones más usados

| Patrón | Significado | Ejemplo |
|--------|-------------|---------|
| `yyyy` | Año (4 cifras) | 2026 |
| `MM` | Mes (2 cifras) | 09 |
| `MMM` / `MMMM` | Mes abreviado / completo | sep / septiembre |
| `dd` | Día del mes (2 cifras) | 11 |
| `E` / `EEEE` | Día de la semana abreviado/completo | vie / viernes |
| `HH` | Hora en formato 24 h | 21 |
| `hh` | Hora en formato 12 h | 09 |
| `mm` | Minutos | 43 |
| `ss` | Segundos | 56 |

## 10.8 Mostrar fechas

Podemos convertir una clase temporal en una **cadena de texto** (proceso inverso a `parse`) usando el formato que nos interese mediante `format()`.

```java
import java.time.*;
import java.time.format.*;

LocalDateTime fechaConHora = LocalDateTime.of(2022, 11, 14, 13, 52, 13);

System.out.println(fechaConHora.format(DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss")));
// 14/11/2022 13:52:13

System.out.println(fechaConHora.format(DateTimeFormatter.ofPattern("EEEE, dd 'de' MMMM 'de' yyyy")));
// lunes, 14 de noviembre de 2022

// Formateadores predefinidos
System.out.println(fechaConHora.toLocalDate().format(DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL)));
```

## 10.9 Tiempo entre fechas

Para obtener la **diferencia entre dos instantes** de tiempo existe la interfaz `java.time.temporal.TemporalUnit`, una enumeración `CronoUnit` y las clases `Period` y `Duration`.

- Mediante **`between()`** obtenemos el tiempo transcurrido entre dos instantes.
- Con **`until()`** también (método de la propia fecha).

### Con LocalDateTime: `between` y `until` devuelven un `long`

```java
LocalDateTime fNacimiento = LocalDateTime.of(1983, Month.MARCH, 24, 20, 30, 15);
LocalDateTime hoy = LocalDateTime.now();

// between con LocalDateTime devuelve long
System.out.println(ChronoUnit.YEARS.between(fNacimiento, hoy));   // 43 años

// until devuelve un long con la unidad indicada
System.out.println(fNacimiento.until(hoy, ChronoUnit.DAYS));      // ~15858 días
System.out.println(fNacimiento.until(hoy, ChronoUnit.HOURS));     // ~380000 horas
```

### Con LocalDate: `between` y `until` devuelven un `Period`

```java
LocalDate finAnio = LocalDate.of(2022, 12, 31);
LocalDate ahora = LocalDate.now(); // 14/11/2022

// until y between con LocalDate devuelven Period
Period hastaFinAnio = ahora.until(finAnio);
System.out.println(hastaFinAnio);            // P1M17D (1 mes y 17 días)
System.out.println("Faltan " + hastaFinAnio.getMonths() + " meses y "
        + hastaFinAnio.getDays() + " días"); // Faltan 1 meses y 17 días

Period vida = Period.between(fNacimiento.toLocalDate(), ahora);
System.out.println(vida.getYears() + " años, " + vida.getMonths() + " meses, "
        + vida.getDays() + " días");
```

!!! tip "Con `Instant` y `Duration` para nanosegundos"
    Para medir **tiempos de ejecución** (el cronómetro del ejercicio 3) conviene `Instant` + `Duration`:

    ```java
    Instant inicio = Instant.now();
    // ... trabajo ...
    Instant fin = Instant.now();
    long milis = Duration.between(inicio, fin).toMillis();
    System.out.println("Han pasado " + milis + " ms");
    ```

## 10.10 Buenas prácticas

- Usa siempre **`java.time`**, nunca `java.util.Date`/`Calendar`.
- Elige el tipo justo: fecha sola → `LocalDate`; fecha+hora → `LocalDateTime`; con zona horaria → `ZonedDateTime`; instante de la máquina → `Instant`.
- Recuerda que son **inmutables**: `plus`, `minus`, `with` devuelven copias.
- Guarda fechas en la BDD como texto **ISO** (`LocalDate.toString()` → `yyyy-MM-dd`), ordenable y sin ambigüedad.
- Para medir tiempos usa `Instant` + `Duration`; para edades, `Period` entre `LocalDate`.

## 10.11 Referencias

- [w3schools: Java Date and Time](https://www.w3schools.com/java/java_date.asp)
- [Oracle: Standard Calendar Tutorial (java.time)](https://docs.oracle.com/javase/tutorial/datetime/)
- [Baeldung: Introduction to Java 8 Date and Time](https://www.baeldung.com/java-8-date-time-intro)
- [Oracle: DateTimeFormatter patterns](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/time/format/DateTimeFormatter.html)

## 10.12 Actividades

1001. **Info de vida**: a partir de una fecha de nacimiento proporcionada, da información de los **años, meses, semanas, días, horas, minutos y segundos** vividos. El programa dirá además cuántos **días del año** quedan y en qué **día de la semana** nació el usuario.

1002. **Memento mori**: se dice que una persona de media vivirá 4000 semanas (unos 80 años). A partir de la fecha de nacimiento, dibuja un **gráfico con las semanas vividas** (marca `X` las semanas vividas y `.` las restantes, en filas de 52).

1003. **Cronómetro**: construye un cronómetro que mida el tiempo de paso por vuelta cada vez que se pulsa **INTRO**: muestra el número de vuelta, segundos y centésimas de cada vuelta; termina al pulsar una tecla distinta.

1004. **Experto en 10.000 horas**: la teoría de las 10.000 horas de Malcolm Gladwell afirma que si practicas una habilidad durante ese tiempo acabas siendo experto. Implementa una función a la que se le pase el número de horas practicadas y devuelva el tiempo restante y una **fecha estimada** en la que se alcanzaría el objetivo practicando `N` horas al día.

1005. **Viernes 13**: crea una función que detecte si **existe un viernes 13** en el mes y año pasados por parámetros (devuelve `true`/`false`). Crea una segunda función que use la anterior para devolver **la fecha del próximo viernes 13**. Pista: usa `TemporalAdjusters.nextOrSame(DayOfWeek.FRIDAY)`.

1006. **Edad exacta**: pide dos fechas (nacimiento y una fecha de referencia) y muestra la edad en años, meses y días, usando `Period.between`.

1007. **Días entre fases**: dado un `LocalDate` inicial, muestra cuántos días, meses y años han pasado hasta hoy usando `ChronoUnit`.
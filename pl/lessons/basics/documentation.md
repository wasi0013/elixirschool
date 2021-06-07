---
version: 0.9.1
title: Dokumentowanie kodu
---

Jak dokumentować kod Elixira.

{% include toc.html %}

## Adnotacje

Nie ważne jak wiele komentarzy piszemy i jak dobra jest dokumentacja, to zawsze jej jakość będzie niewystarczająca. Pomimo to wszyscy wiemy, że dokumentacja jest istotna zarówno dla nas samych, jak i dla osób, które pracują lub będą pracować z naszym kodem.

Elixir traktuje dokumentację jako byt podstawowy (ang. *first-class citizen*), oferując zestaw funkcji pozwalających na generowanie i pracę z dokumentacją w projekcie. Elixir dostarcza wiele różnych rozwiązań na poziomie kodu do tworzenia dokumentacji. Przyjrzyjmy się trzem z nich: 

  - `#` - Służy do komentowania kodu.
  - `@moduledoc` - Pozwala na tworzenie dokumentacji modułów.
  - `@doc` - Pozwala na tworzenie dokumentacji poszczególnych funkcji.

### Komentowanie kodu

Najprostszą metodą dokumentowania kodu są komentarze. Podobnie jak w Pythonie i Ruby, tak w Elixirze komentarz rozpoczyna się znakiem `#`, zwanym *kratką* albo *hashem*.

Popatrzymy na ten skrypt (greeting.exs):

```elixir
# Outputs 'Hello, chum.' to the console.
IO.puts("Hello, " <> "chum.")
```

Gdy go uruchomimy, Elixir zignoruje wszystko od znaku `#` aż do końca linii, traktując jako nieistotny i nieinterpretowany element. Komentarz nie ma żadnej wartości ani nie wpływa na szybkość wykonania kodu. Jednakże pozwala innym zrozumieć nasz kod. Tego rodzaju komentarze powinny być używane z umiarem, ponieważ w dużej liczbie zaśmiecają kod i zamiast pomagać, mogą przeszkadzać. Niektórzy programiści w ogóle nie uważają tego rodzaju komentarzy za przydatne.

### Dokumentowanie modułów

Adnotacja `@moduledoc` służy do dokumentowania modułów. Zazwyczaj umieszcza się ją w linii poniżej deklaracji `defmodule` na górze pliku. Poniższy przykład ilustruje użycie `@moduledoc`.

```elixir
defmodule Greeter do
  @moduledoc """
  Provides a function `hello/1` to greet a human
  """

  def hello(name) do
    "Hello, " <> name
  end
end
```

Dostęp do dokumentacji modułu w IEx jest możliwy z pomocą polecenia `h`.

```elixir
iex> c("greeter.ex", ".")
[Greeter]

iex> h Greeter

                Greeter

Provides a function hello/1 to greet a human
```

### Dokumentowanie funkcji

Elixir poza adnotacją pozwalającą na dokumentowanie modułów ma też adnotację służącą do dokumentowania poszczególnych funkcji. Adnotacja `@doc` jest umieszczana nad dokumentowaną funkcją.

```elixir
defmodule Greeter do
  @moduledoc """
  ...
  """

  @doc """
  Prints a hello message

  ## Parameters

    - name: String that represents the name of the person.

  ## Examples

      iex> Greeter.hello("Sean")
      "Hello, Sean"

      iex> Greeter.hello("pete")
      "Hello, pete"

  """
  @spec hello(String.t()) :: String.t()
  def hello(name) do
    "Hello, " <> name
  end
end
```

W IEx za pomocą polecenia `h` możemy też dostać się do dokumentacji funkcji.

```elixir
iex> c("greeter.ex")
[Greeter]

iex> h Greeter.hello

                def hello(name)

`hello/1` prints a hello message

Parameters

  • name: String that represents the name of the person.

Examples

    iex> Greeter.hello("Sean")
    "Hello, Sean"

    iex> Greeter.hello("pete")
    "Hello, pete"

iex>
```

Zauważ jak za pomocą znaczników, umożliwiliśmy terminalowi sformatowanie dokumentacji. Poza tym, że fajnie to wygląda i jest nowatorskim rozwiązaniem w ekosystemie Elixira, to daje nam duże możliwości przy generowaniu HTML-a z pomocą narzędzia ExDoc.

## ExDoc

ExDoc jest oficjalnym projektem zespołu Elixira służącym do **generowania HTML-a (HyperText Markup Language) i dokumentacji online dla projektów tworzonych w Elixirze**, które można znaleźć na [GitHub](https://github.com/elixir-lang/ex_doc). Na początek stwórzmy nowy projekt w mix-ie:

```bash
$ mix new greet_everyone

* creating README.md
* creating .gitignore
* creating mix.exs
* creating lib
* creating lib/greet_everyone.ex
* creating test
* creating test/test_helper.exs
* creating test/greet_everyone_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd greet_everyone
    mix test

Run "mix help" for more commands.

$ cd greet_everyone

```

Następnie skopiujmy adnotację `@doc` do pliku `lib/greeter.ex` i upewnijmy się, że nadal działa on z linii poleceń. Jako że pracujemy z mixem uruchommy IEx korzystając z polecenia `iex -S mix` i wpiszmy:

```bash
iex> h Greeter.hello

                def hello(name)

Prints a hello message

Parameters

  • name: String that represents the name of the person.

Examples

    iex> Greeter.hello("Sean")
    "Hello, Sean"

    iex> Greeter.hello("pete")
    "Hello, pete"
```

### Instalacja

Wnioskując z powyższego komunikatu jesteśmy już gotowi by skonfigurować ExDoc. W pliku `mix.exs` musimy dodać dwie zależności `:earmark` i `:ex_doc`.

```elixir
def deps do
  [{:earmark, "~> 0.1", only: :dev}, {:ex_doc, "~> 0.11", only: :dev}]
end
```

Podając opcję `only: :dev` mówimy mixowi, że nie chcemy pobierać i kompilować tych zależności na środowisku innym niż deweloperskie. Ale czym jest Earmark? Earmark jest to parser znaczników napisany dla Elixira, który służy ExDocowi  na zamianę dokumentacji z adnotacji `@moduledoc` i `@doc` na elegancki kod HTML.

Oczywiście nie jesteśmy ograniczeni tylko do Earmarka. Jak chcesz możesz zamienić go na Pandoc, Hoedown albo Cmark; to wymaga trochę dodatkowej konfiguracji, o której poczytasz [tutaj (ang.)](https://github.com/elixir-lang/ex_doc#changing-the-markdown-tool). Na potrzeby tej lekcji pozostaniemy jednak przy Earmarku.

### Generowanie dokumentacji

Idąc dalej w linii poleceń wydajemy dwie komendy:

```bash
$ mix deps.get # pobiera ExDoc + Earmark.
$ mix docs # generuje dokumentację.

Docs successfully generated.
View them at "doc/index.html".
```

Jeżeli wszystko poszło zgodnie z planem powinieneś zobaczyć komunikat taki jak wyżej. Jeżeli zajrzymy teraz do naszego projektu to w katalogu **doc/** odnajdziemy wygenerowaną dokumentację. Jeżeli otworzysz plik `index.html` w przeglądarce powinieneś zobaczyć:

![ExDoc Screenshot 1]({% asset documentation_1.png @path %})

Jak widać Earmark stworzył z naszych znaczników dokumentację, a ExDoc wyświetla ją w czytelny sposób.

![ExDoc Screenshot 2]({% asset documentation_2.png @path %})

Możemy ją teraz umieścić na GitHubie, naszej stronie, albo w serwisie [HexDocs](https://hexdocs.pm/).

## Dobre praktyki

Zasady tworzenia dokumentacji powinny być opisane w przewodniku dobrych praktyk języka. Jednakże Elixir jest jeszcze bardzo młodym językiem i wiele elementów jego ekosystemu musi zostać zestandaryzowanych, co oznacza, że dobre praktyki dla dokumentacji są tworzone przede wszystkim przez społeczność programistów. Można o tym poczytać, w dokumencie [TheElixir Style Guide](https://github.com/niftyn8/elixir_style_guide).

  - Zawsze dokumentuj moduły.

```elixir
defmodule Greeter do
  @moduledoc """
  This is good documentation.
  """

end
```

  - Jeżeli nie załączasz dokumentacji, to **nie** zostawiaj jej pustej. Dopisz `false` do adnotacji:

```elixir
defmodule Greeter do
  @moduledoc false

end
```

 - Jak odwołujesz się do funkcji w dokumentacji modułu, używaj apostrofów:

```elixir
defmodule Greeter do
  @moduledoc """
  ...

  This module also has a `hello/1` function.
  """

  def hello(name) do
    IO.puts("Hello, " <> name)
  end
end
```

 - Oddziel kod od `@moduledoc` pozostawiając wolną linię:

```elixir
defmodule Greeter do
  @moduledoc """
  ...

  This module also has a `hello/1` function.
  """

  alias Goodbye.bye_bye
  # and so on...

  def hello(name) do
    IO.puts("Hello, " <> name)
  end
end
```

 - Używaj znaczników w dokumentowaniu funkcji by łatwiej było czytać ją w  IEx lub ExDoc.

```elixir
defmodule Greeter do
  @moduledoc """
  ...
  """

  @doc """
  Prints a hello message

  ## Parameters

    - name: String that represents the name of the person.

  ## Examples

      iex> Greeter.hello("Sean")
      "Hello, Sean"

      iex> Greeter.hello("pete")
      "Hello, pete"

  """
  @spec hello(String.t()) :: String.t()
  def hello(name) do
    "Hello, " <> name
  end
end
```

 - Podawaj przykłady w dokumentacji. Pozwoli to też na wygenerowanie testów automatycznych na ich podstawie z użyciem [ExUnit.DocTest][]. By to zrobić, należy wywołać makro `doctest/1`, lecz dokumentacja musi spełniać pewne warunki opisane w [oficjalniej dokumentacji][ExUnit.DocTest] narzędzia.

[ExUnit.DocTest]: https://hexdocs.pm/ex_unit/ExUnit.DocTest.html

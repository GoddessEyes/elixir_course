# Конфигурация приложения

Каждое приложение имеет свои собственные настройки и АПИ для доступа к ним.

```
$ iex -S mix
iex(1)> Application.get_all_env(:my_cool_app)
[]
iex(2)> Application.put_env(:my_cool_app, :param1, 42)
:ok
iex(4)> Application.put_env(:my_cool_app, :param2, :some_val)
:ok
iex(5)> Application.get_all_env(:my_cool_app)                
[param1: 42, param2: :some_val]

iex(7)> Application.get_env(:my_cool_app, :param1)
42
iex(8)> Application.fetch_env(:my_cool_app, :param1)
{:ok, 42}
iex(9)> Application.fetch_env(:my_cool_app, :param3)
:error
```

Настройки хранятся в application environment, это быстрое in-memory key-value хранилище. Оно находится в глобальной области видимости, и к нему можно обращаться из любого места в коде.

В нашем приложении пока нет настроек, их можно добавить в **mix.exs**:
```
def application do
  [
    extra_applications: [:logger],
    env: [param1: 42, param2: "hello"]
  ]
end
```

И тогда настройки появятся сразу после запуска приложения:
```
$ iex -S mix
iex(1)> Application.get_all_env(:my_cool_app)
[param1: 42, param2: "hello"]
```

Однако настройки удобнее задавать не в mix.exs, а в отдельных файлах. mix позволяет указать настройки в файле **config/config.exs**:

```
import Config

config :my_cool_app,
  param1: 42,
  param2: "hello",
  param3: true
```

Настройки в этом файле переопределяют настройки в mix.exs:
```
iex(1)> Application.get_all_env(:my_cool_app)
[param1: 42, param3: true, param2: "hello"]
```

Давайте запустим в нашем приложении PathFinder и ShardingAgent, а нужные для них настройки вынесем в config.exs.

TODO описать все сделанное.

```
$ iex -S mix
Compiling 3 files (.ex)
Generated my_cool_app app
init PathFinder %{data_file: ["/home/y_zhloba/p/elixir_course_junior/lesson_11/my_cool_app/_build/dev/l
ib/my_cool_app/priv/cities.csv"]}
iex(1)> MyCoolApp.PathFinder.get_route("Москва", "Казань")
{:ok, ["Москва", "Архангельск", "Казань"], 3267}
```

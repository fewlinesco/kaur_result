# Kaur.Result

`Kaur.Result` is a part of [Kaur](https://github.com/fewlinesco/kaur).
This library exposes a module to manipulate, in pipelines, couples
`{:ok, value}` and `{:error, error_value}`.

Its implementation is strongly inspired by the abstract data type
`Either`, in the Haskell world.

## Installation

```elixir
def deps do
  [
    {:kaur_result, "~> 1.0.0"}
  ]
end

```

## Usage

`{:ok, value}` and `{:error, reason}` is a common pattern in Erlang
and Elixir. The `Kaur.Result` module adds functions to help deal
with these values without getting out of your pipeline.

In this example we try to determine if a person can rent a car.
People can rent a car if they are between 21 and 99 year old and
have a bonus greater than 0.8.

Here is a module that implements the business logic of our
application:

```elixir
defmodule Person do
  defstruct [:name, :age, :bonus]
end

defmodule MyModule do

  defp display_driving_message(person) do
    "Welcome #{person.name}, you can rent a car"
  end

  defp handle_error(person, {:bonus, expected_bonus}) do
    "Sorry #{person.name}, but you need a bonus of #{expected_bonus} but have only #{person.bonus}."
  end
  defp handle_error(person, {:license_type, expected_license}) do
    "Sorry #{person.name}, but you need a #{expected_license} license but have a #{person.license_type} license."
  end
  defp handle_error(person, {:too_old, maximum_age}) do
    "Sorry #{person.name}, but you need to be younger than #{maximum_age}"
  end
  defp handle_error(person, {:too_young, minimum_age}) do
    "Sorry #{person.name}, but you need to be older than #{minimum_age}"
  end

  defp validate_age(%{age: age}) when age > 99, do: {:error, {:too_old, 99}}
  defp validate_age(%{age: age}) when age < 21, do: {:error, {:too_young, 21}}
  defp validate_age(person), do: {:ok, person}

  defp validate_bonus(person = %{bonus: bonus}) when bonus > 0.8, do: {:ok, person}
  defp validate_bonus(_person), do: {:error, {:bonus, 0.8}}
end
```

### Example without Kaur

```elixir
def rent_a_car(person = %Person{}) do
  with {:ok, person1} <- validate_age(person),
       {:ok, person2} <- validate_bonus(person1)
  do
    {:ok, display_driving_message(person2)}
  else
    {:error, reason} ->
      {:error, handle_error(person, reason)}
  end
end
```

### Example using Kaur.Result

```elixir
alias Kaur.Result

def rent_a_car(person = %Person{}) do
  person
  |> validate_age()
  |> Result.and_then(&validate_bonus/1)
  |> Result.map(&display_driving_message/1)
  |> Result.map_error(&handle_error(person, &1))
end
```


Execution

```
iex> MyModule.rent_a_car %Person{name: "Jane", age: 42, bonus: 0.9}
{:ok, "Welcome Jane, you can rent a car"}

iex> MyModule.rent_a_car %Person{name: "John", age: 42, bonus: 0.5}
{:error, "Sorry John, but you need a bonus of 0.8 but have only 0.5."}

iex> MyModule.rent_a_car %Person{name: "Robert", age: 11, bonus: 0.9}
{:error, "Sorry Robert, but you need to be older than 21"}

iex> MyModule.rent_a_car %Person{name: "Mary", age: 122, bonus: 0.8}
{:error, "Sorry Mary, but you need to be younger than 99"}
```


## Code of Conduct

By participating in this project, you agree to abide by its [CODE OF CONDUCT](CODE_OF_CONDUCT.md).

## Contributing

You can see the specific [CONTRIBUTING](CONTRIBUTING.md) guide.

## License

Kaur.Result is released under [The MIT License (MIT)](https://opensource.org/licenses/MIT).

# Countdown Script — Unexpected Test Behavior

## Description of the Problem

While completing the **Build Five Programs** workshop an unexpected test was running with an error response.
As instructed, the `five.sh` script needs to run other four programs created previously:

```bash
./questionnaire.sh
./countdown.sh 3
./bingo.sh
./fortune.sh
```

These commands were added to `five.sh` in the suggested order, but the workshop test did not pass.

## Investigation

After some struggle I decided to check the actual test file to understand the errous response, even after typing in the hint (solution) provided in this particular task.
I inspected the test file at `.freeCodeCamp/test.sh` and found the following commands:

```bash
#!/bin/bash
# Program to run my other four programs

./questionnaire.sh
./countdown.sh a
./bingo.sh
./fortune.sh
```

The test file called `countdown.sh` with the argument `a`, whereas the workshop "Get a Hint" section explicitly suggested using `3` in `five.sh`.
This meant that the test was invoking the countdown script with a non-numeric argument, which could expose a problem with its input validation.

## Changes to `countdown.sh`

The original countdown logic used a numeric comparison:

```bash
if [[ $1 -gt 0 ]]
```

I temporarily commented out that implementation while investigating the behavior. I then added a regular-expression check to accept positive integers:

```bash
if [[ $1 =~ ^[1-9][0-9]*$ ]]; then
  I=$1
  while [[ $I -ge 0 ]]
  do
    echo $I
    (( I-- ))
    sleep 1
  done
else
  echo "Include a positive integer as the first argument."
fi
```

With the help of artifitial intellengence a correction was made to the comparison expression `^[1-9][0-9]*$`.
This matches to positive integers that begin with a digit from 1 to 9, followed by zero or more digits. 
Inputs such as `3` are accepted, while `a`, `0`, and negative numbers are rejected.

## Expected Behavior

- Running `./countdown.sh 3` should print `3`, `2`, `1`, and `0`.
- Running `./countdown.sh a` should display an error message asking for a positive integer (the one run by the test file).
- The `five.sh` script should still call the four programs in the order specified by the workshop.

## Conclusion

The investigation revealed a difference between the workshop's suggested command (`./countdown.sh 3`) and the command in the test file (`./countdown.sh a`).

The input-validation change was an attempt to handle the invalid argument safely and effectively was enough to pass the test of this particular stage. However, this did not seem included in the scope of the project, although protection and validation is important in projects with reading input from users.

The exact reason the workshop test failed was not confirmed. 
The test file's use of `a` may be intentional to check how the countdown script handles invalid input, rather than evidence that the test itself is faulty.
---
type: security
---
# ReDoS - [[Regular expression]] Denial of Service

The Regular expression Denial of Service (ReDoS) is a Denial of Service attack, that exploits the fact that most Regular Expression implementations may reach extreme situations that cause them to work very slowly. An attacker can then cause a program using a Regular Expression (Regex) to enter these extreme situations and then hang for a very long time.

The Regex naïve algorithm builds a [Nondeterministic Finite Automaton (NFA)](https://en.wikipedia.org/wiki/Nondeterministic_finite_state_machine), which is a finite state machine where for each pair of state and input symbol there may be several possible next states. Then the engine starts to make transition until the end of the input. Since there may be several possible next states, a deterministic algorithm is used. This algorithm tries one by one all the possible paths (if needed) until a match is found (or all the paths are tried and fail).

The root cause is in a Regex enginre feature called backtracking, if the input (token) fails to match, the engine goes back to previous positions where it could take a different path.

Notice, that not all algorithms are naïve, and actually Regex algorithms can be written in an efficient way. Unfortunately, most Regex engines today try to solve not only “pure” Regexes, but also “expanded” Regexes with “special additions”, such as back-references that cannot be always be solved efficiently

## Evil Regex

A Regex pattern is called Evil Regex if it can get stuck on crafted input.

Evil Regex contains:
- Grouping with repetition
- Inside the repeated group:
  - Repetition
  - Alternation with overlapping
Examples of Evil Regex:
- `(a+)+`
- `([a-zA-Z]+)*`
- `(a|aa)+`
- `(a|a?)+`
- `(.*a){x} for x \> 10`

All the above are susceptible to the input `aaaaaaaaaaaaaaaaaaaaaaaa!` (The minimum input length might change slightly, when using faster or slower machines).

## Examples

### Vulnerable Regex in online repositories

1. [ReGexLib,id=1757 (email validation)](https://regexlib.com/REDetails.aspx?regexp_id=1757&AspxAutoDetectCookieSupport=1) - see `(([\-.]|[_]+)?([a-zA-Z0-9]+))*`, which is an Evil Regex
    
    `^([a-zA-Z0-9])(([\-.]|[_]+)?([a-zA-Z0-9]+))*(@){1}[a-z0-9]+[.]{1}(([a-z]{2,3})|([a-z]{2,3}[.]{1}[a-z]{2,3}))$`

    Input: `aaaaaaaaaaaaaaaaaaaaaaaa!`

2. OWASP Validation Regex Repository, Java Classname - see `(([a-z])+.)+` part, which is an Evil Regex
    
    `^(([a-z])+.)+[A-Z]([a-z])+$`
    
    Input: `aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa!`

## Web application attack

- Open a JavaScript
- Find Evil Regex
- Craft a malicious input for the found Regex
- Submit a valid value via intercepting proxy
- Change the request to contain a malicious input
- You are done!

## ReDoS via Regex Injection

The following example checks if the username is part of the password entered by the user.

```java
String userName = textBox1.Text;
String password = textBox2.Text;
Regex testPassword = new Regex(userName);
Match match = testPassword.Match(password);
if (match.Success)
{
    MessageBox.Show("Do not include name in password.");
}
else
{
    MessageBox.Show("Good password.");
}
```

If an attacker enters `^(([a-z])+.)+[A-Z]([a-z])+$` as a username and `aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa!` as a password, the program will hang.

## Read more

- [OWASP ReDoS](https://owasp.org/www-community/attacks/Regular_expression_Denial_of_Service_-_ReDoS)
- [Patterns for non-regular languages](https://en.wikipedia.org/wiki/Regular_expression#Patterns_for_non-regular_languages)
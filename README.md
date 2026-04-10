# BuT - Build Tool
The idea is to combine [make](https://www.gnu.org/software/make/) and [yay](https://github.com/jguer/yay).
Why combining them? Because both can be used to build stuff.
Make can be used to automate running builders, yay can be used to automate running wget and make commands.
Why not having a single tool to just get thing built? That's the idea.

## Nix exists
1. Nix is for FP nerds.
2. Nobody (except FP nerds) knows how it really works. It somehow runs
   its expressions and builds some kind of derivations (what even is it?)
   and usually you just need to follow the guides (written by FP nerds) and believe it will work.
   At least that is my expirience.

But it is a good idea to borrow all the good stuff from nix (the only good stuff there is caching).

## How BuT works
### The make part
Suppose we have the following rules in `butfile`:
```
but: but.o
  gcc but.o -o but

but.o: @local but.c
  gcc -c but.c -o but.o
```
And the user runs `but but` the first time. Then, the following happens:
1. `but.c` resolves to the full path `/home/user/but/but.c` everywhere in the rule
2. `but.o` resolves to `target` everywhere in the rule
3. The hash of the whole rule is taken: 123
4. `target` resolves to `/home/user/.cache/but/123` everywhere in the rule
6. In an empty tmp folder, the following runs:
   `gcc -c /home/user/but/but.c -o /home/user/.cache/but/123` 
7. In the rule above, `but.o` resolves to `/home/user/.cache/but/123`
8. `but` resolves to  `target` in the rule above
9. The hash of the rule above is taken: 456
10. `target` resolves to `/home/user/.cache/but/456` in the rule above
11. In an empty tmp folder, the following runs:
   `gcc /home/user/.cache/but/123 -o /home/user/.cache/but/456`
12. `/home/user/.cache/but/456` is copied to `/home/user/but/but`

Obviously all in the `.cache` folder is cached and not built again if not required.

### The yay part 
_work in progress_

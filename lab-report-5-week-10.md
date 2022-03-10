# Lab Report 5
## Finding Test Cases
In order to find the differneces between my implementation of MarkdownParse and the provided MarkdownParse, I:
1. Copied all of the test cases and bash script from the provided MarkdownParse to my implementation, using th `cp -r` and `cp` commands.
2. Ran each implementation and saved rests into a `results.txt` file in each respective folder, using the command `bash scrips.sh > results.txt` in each respective folder. 
3. Used the `diff` command, with the two `result.txt` files as arguments.
4. Chose 2 test cases from the list that I thought looked interesting
 
---

## Test File 22

Contents of the file:
```
[foo](/bar\* "ti\*tle")

```
- My implementation's output: `[/bar\* "ti\*tle"]`
- Provided implementation's output: `[]`
- Expected output: `[/bar\*]`
- For this test case **neither** implementation is correct.

The problem in my implementation is that it does not consider that there could be a title within the link text (between the parentheses). We should change [this part of the code:](https://github.com/ocboogie/markdown-parse/blob/44eb576d33ffcd122e2c325b17d926ff0b420403/MarkdownParse.java#L47)
```
if (nextCloseBracket + 1 == openParen) {
    toReturn.add(markdown.substring(openParen + 1, closeParen));
}
```
Any if there are quotes (") at the end of the link, we need to substring out any text between quotes from the link text. We should also trim the link before and after removing the title.

---

## Test File 511

Contents of the file:
```
[link [foo [bar]]](/uri)
```
- My implementation's output: `[]`
- Provided implementation's output: `[/uri]`
- Expected output: `[/uri]`
- For this test case, the provided implementation is correct.

The problem with my implemetation is that it only identifies the first close bracket, and does not consider that pairs of open and close brackets could be part of the displayed title part of the quote. Since it disregards all potential quotes if the index of the open parenthesis is not one more than the close bracket, and it identifies the first close bracket, it does not even consider it a link. We should change [this part of the code:](https://github.com/ocboogie/markdown-parse/blob/44eb576d33ffcd122e2c325b17d926ff0b420403/MarkdownParse.java#L15)
```
int nextOpenBracket = markdown.indexOf("[", currentIndex);
    if (nextOpenBracket == -1) {
        break;
    }
    if (nextOpenBracket != 0 && markdown.charAt(nextOpenBracket - 1) == '!') {
        currentIndex = nextOpenBracket + 1;
        continue;
    }
    int nextCloseBracket = markdown.indexOf("]", nextOpenBracket);
```
We should implement a counter that counts the `n` number of open brackets, as we transverse through the string, and then finds the `n`th close bracket (if it exists). 

针针见血，怎么消除JavaScript中的代码坏味道（1） 

## 常见的代码坏味道

### 代码很绕的坏味道

#### 上下文：
如果单词以辅音开头（或辅音集），把它剩余的步伐移到前面，并且添加上『ay』如pig -> igpay
如果单词以元音开头，保持顺序但是在结尾加上『way』如，egg->eggway等

#### 糟糕代码：
```js
/* const */ var CONSONANTS = 'bcdfghjklmnpqrstvwxyz';
/* const */ var VOWELS = 'aeiou';

function englishToPigLatin(english) {
  /* const */ var SYLLABLE = 'ay';
  var pigLatin = '';

  if (english !== null && english.length > 0 &&
    (VOWELS.indexOf(english[0]) > -1 ||
    CONSONANTS.indexOf(english[0]) > -1 )) {
    if (VOWELS.indexOf(english[0]) > -1) {
      pigLatin = english + SYLLABLE;
    } else {
      var preConsonants = '';
      for (var i = 0; i < english.length; ++i) {
        if (CONSONANTS.indexOf(english[i]) > -1) {
          preConsonants += english[i];
          if (preConsonants == 'q' &&
            i+1 < english.length && english[i+1] == 'u') {
            preConsonants += 'u';
            i += 2;
            break;
          }
        } else { break; }
      }
      pigLatin = english.substring(i) + preConsonants + SYLLABLE;
    }
  }

  return pigLatin;
}
```

#### 问题在哪：

- 太多语句
- 太多嵌套
- 太高复杂度

#### 检测出问题：

关于Lint的配置项：如最大语句数，复杂度，最大嵌套数，最大长度，最多传参，最多嵌套回调

```sh
/*jshint maxstatements:15, maxdepth:2, maxcomplexity:5 */
/*eslint max-statements:[2, 15], max-depth:[1, 2], complexity:[2, 5] */
```

```sh
7:0 - Function 'englishToPigLatin' has a complexity of 7.
7:0 - This function has too many statements (16). Maximum allowed is 15.
22:10 - Blocks are nested too deeply (5).
```

#### 测试现行：

```js
describe('Pig Latin', function() {
  describe('Invalid', function() {
    it('should return blank if passed null', function() {
      expect(englishToPigLatin(null)).toBe('');
    });

    it('should return blank if passed blank', function() {
      expect(englishToPigLatin('')).toBe('');
    });

    it('should return blank if passed number', function() {
      expect(englishToPigLatin('1234567890')).toBe('');
    });

    it('should return blank if passed symbol', function() {
      expect(englishToPigLatin('~!@#$%^&*()_+')).toBe('');
    });
  });

  describe('Consonants', function() {
    it('should return eastbay from beast', function() {
      expect(englishToPigLatin('beast')).toBe('eastbay');
    });

    it('should return estionquay from question', function() {
      expect(englishToPigLatin('question')).toBe('estionquay');
    });

    it('should return eethray from three', function() {
      expect(englishToPigLatin('three')).toBe('eethray');
    });
  });

  describe('Vowels', function() {
    it('should return appleay from apple', function() {
      expect(englishToPigLatin('apple')).toBe('appleay');
    });
  });
});
```


#### 重构后代码：

```js
const CONSONANTS = ['th', 'qu', 'b', 'c', 'd', 'f', 'g', 'h', 'j', 'k',
'l', 'm', 'n', 'p', 'q', 'r', 's', 't', 'v', 'w', 'x', 'y', 'z'];
const VOWELS = ['a', 'e', 'i', 'o', 'u'];
const ENDING = 'ay';

let isValid = word => startsWithVowel(word) || startsWithConsonant(word);
let startsWithVowel = word => !!~VOWELS.indexOf(word[0]);
let startsWithConsonant = word => !!~CONSONANTS.indexOf(word[0]);
let getConsonants = word => CONSONANTS.reduce((memo, char) => {
  if (word.startsWith(char)) {
    memo += char;
    word = word.substr(char.length);
  }
  return memo;
}, '');

function englishToPigLatin(english='') {
   if (isValid(english)) {
      if (startsWithVowel(english)) {
        english += ENDING;
      } else {
        let letters = getConsonants(english);
        english = `${english.substr(letters.length)}${letters}${ENDING}`;
      }
   }
   return english;
}
```

#### 数据对比：

max-statements: 16 → 6
max-depth: 5 → 2
complexity: 7 → 3
max-len: 65 → 73
max-params: 1 → 2
max-nested-callbacks: 0 → 1

#### 相关资源：
jshint - http://jshint.com/
eslint - http://eslint.org/
jscomplexity - http://jscomplexity.org/
escomplex - https://github.com/philbooth/escomplex
jasmine - http://jasmine.github.io/

Reference: http://elijahmanor.com/talks/js-smells/#/0/3



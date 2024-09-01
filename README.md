# hy_binder_notebook_setup

```bash
!pip install requests hy
```

```python
import requests

r = requests.get("https://rentry.co/fv9zo8wx/raw")
print(r.text)

with open("hy.txt", "w") as file:
    file.write(r.text)
```

    hy 
    
    ```hy
    ; セミコロンによるコメントは、他のLispと同じです
    
    ;; S式の基本
    ; Lispプログラムは、シンボリックな表現（S式）で構成され、
    ; これは次のように見えます
    (some-function args)
    ; さて、代表的な「Hello World」の例です
    (print "hello world")
    
    ;; 単純なデータ型
    ; 全ての単純なデータ型は、Pythonの対応するものと同じです
    42 ; => 42
    3.14 ; => 3.14
    True ; => True
    4+10j ; => (4+10j) 複素数
    
    ; それでは、簡単な算術演算から始めましょう
    (+ 4 1) ;=> 5
    ; 演算子は、他のLispと同様に、全ての引数に適用されます
    (+ 4 1 2 3) ;=> 10
    (- 2 1) ;=> 1
    (* 4 2) ;=> 8
    (/ 4 1) ;=> 4
    (% 4 2) ;=> 0 剰余演算子
    ; べき乗は、Pythonと同様に ** 演算子で表されます
    (** 3 2) ;=> 9
    ; 入れ子の式も期待通りに動作します
    (+ 2 (* 4 2)) ;=> 10
    ; 論理演算子や比較演算子も同様に動作します
    (= 5 4) ;=> False
    (not (= 5 4)) ;=> True
    
    ;; 変数
    ; 変数は setv を使って設定されます。変数名には ()[]{}",'`;#| を除き
    ; UTF-8が使用可能です
    (setv a 42)
    (setv π 3.14159)
    (def *foo* 42)
    ;; 他のコンテナデータ型
    ; 文字列、リスト、タプル、辞書
    ; これらは、Pythonのコンテナ型と全く同じです
    "hello world" ;=> "hello world"
    ; 文字列操作はPythonと同様に機能します
    (+ "hello " "world") ;=> "hello world"
    ; リストは [] で作成され、インデックスは 0 から始まります
    (setv mylist [1 2 3 4])
    ; タプルは変更不可なデータ構造です
    (setv mytuple (, 1 2))
    ; 辞書はキーと値のペアです
    (setv dict1 {"key1" 42 "key2" 21})
    ; :name を使ってキーワードを定義でき、辞書のキーとして使用できます
    (setv dict2 {:key1 41 :key2 20})
    ; `get` を使って、インデックス/キーの要素を取得します
    (get mylist 1) ;=> 2
    (get dict1 "key1") ;=> 42
    ; キーワードが使用されている場合は、それを直接呼び出せます
    (:key1 dict2) ;=> 41
    
    ;; 関数とその他のプログラム構造
    ; 関数は defn を使って定義され、最後のS式がデフォルトで返されます
    (defn greet [name]
      "簡単な挨拶" ; オプションのドキュメント文字列
      (print "hello " name))
    
    (greet "bilbo") ;=> "hello bilbo"
    
    ; 関数はオプション引数やキーワード引数を取ることができます
    (defn foolists [arg1 &optional [arg2 2]]
      [arg1 arg2])
    
    (foolists 3) ;=> [3 2]
    (foolists 10 3) ;=> [10 3]
    
    ; さらに、可変長引数やキーワード引数を使用することもできます
    (defn something-fancy [wow &rest descriptions &kwargs props]
      (print "Look at" wow)
      (print "It's" descriptions)
      (print "And it also has:" props))
    
    (something-fancy "My horse" "amazing" :mane "spectacular")
    
    ; `apply` を使って、スプラット演算子の代わりに引数を適用します
    (apply something-fancy ["My horse" "amazing"] { "mane" "spectacular" })
    
    ; 無名関数は `fn` または `lambda` を使って作成され、`defn`と似ています
    (map (fn [x] (* x x)) [1 2 3 4]) ;=> [1 4 9 16]
    
    ;; シーケンス操作
    ; Hyには、シーケンス操作用の組み込みユーティリティがあります
    ; `first` または `car` を使って、最初の要素を取得します
    (setv mylist [1 2 3 4])
    (setv mydict {"a" 1 "b" 2})
    (first mylist) ;=> 1
    
    ; `cut` を使ってリストをスライスします
    (cut mylist 1 3) ;=> [2 3]
    
    ; `get` を使って、リストまたは辞書から要素を取得します
    (get mylist 1) ;=> 2
    (get mydict "b") ;=> 2
    ; リストのインデックスはPythonと同様に0から始まります
    ; `assoc` を使ってキー/インデックスに要素を設定します
    (assoc mylist 2 10) ; mylistを [1 2 10 4] にします
    (assoc mydict "c" 3) ; mydictを {"a" 1 "b" 2 "c" 3} にします
    ; シーケンス操作を楽しくする他のコア関数もたくさんあります
    
    ;; Pythonとの相互運用
    ;; `import` はPythonと同様に機能します
    (import datetime)
    (import functools [partial reduce]) ; functoolsからpartialとreduceをインポート
    (import matplotlib.pyplot :as plt) ; fooをbarとしてインポート
    ; すべての組み込みPythonメソッドなどはHyからアクセス可能です
    ; a.foo(arg) は (.foo a arg) のように呼び出します
    (.split (.strip "hello world  ")) ;=> ["hello" "world"]
    
    ; "スレッドマクロ" と呼ばれる、値に対して複数の関数を実行するための
    ; ショートカットがあります。これは矢印で示されます:
    (-> "hello world  " (.strip) (.split)) ;=> ["hello" "world]
    ; 矢印は、呼び出しごとに値を最初の引数として渡します。例えば:
    (-> 4 (* 3) (+ 2))
    ; は次のようになります:
    (+ (* 4 3) 2)
    
    ; 値を2番目の引数として渡す "スレッドテールマクロ" もあります。比較してみてください:
    (-> 4 (- 2) (+ 1)) ;=> 3
    (+ (- 4 2) 1) ;=> 3
    ; これに対して:
    (->> 4 (- 2) (+ 1)) ;=> -1
    (+ 1 (- 2 4)) ;=> -1
    
    ;; 条件分岐
    ; (if condition (trueの場合の処理) (falseの場合の処理))
    (if (= passcode "moria")
      (print "welcome")
      (print "Speak friend, and Enter!"))
    
    ; 複数のifやelse ifの条件を `cond` でグループ化します
    (cond
      (= someval 42) (print "Life, universe and everything else!")
      (> someval 42) (print "val too large")
      (< someval 42) (print "val too small"))
    
    ; `do` でステートメントをグループ化し、これらは順次実行されます
    ; `defn` などのフォームには暗黙の `do` が含まれます
    (do
      (setv someval 10)
      (print "someval is set to " someval)) ;=> 10
    
    ; `let` でレキシカルバインディングを作成し、これらで定義された変数は
    ; ローカルスコープを持ちます
    (let [nemesis {"superman" "lex luther"
                    "sherlock" "moriarty"
                    "seinfeld" "newman"}]
      (for [[h v] (.items nemesis)]
        (print (.format "{0}'s nemesis was {1}" h v))))
    
    ;; クラス
    ; クラスは次のように定義されます
    (defclass Wizard [object]
      (defn __init__ [self spell]
        (setv self.spell spell))
    
      (defn get-spell [self]
        self.spell))
    ```



```python
%%writefile hello.hy

; セミコロンによるコメントは、他のLispと同じです

;; S式の基本
; Lispプログラムは、シンボリックな表現（S式）で構成され、
; これは次のように見えます
;(some-function args)
; さて、代表的な「Hello World」の例です
(print "hello world")
```

    Writing hello.hy



```python
!hy2py hello.hy
```

    import hy
    print('hello world')



```python
%%writefile test_requests.hy

(import requests)

(setv r (.get requests "https://rentry.co/fv9zo8wx/raw"))
(cond
  (= r.status_code 200)
    (for [line (.split r.text "\n")]
      (print line))
  True
    (print "error"))
```

    Overwriting test_requests.hy



```python
!hy test_requests.hy
```

    hy 
    
    ```hy
    ; セミコロンによるコメントは、他のLispと同じです
    
    ;; S式の基本
    ; Lispプログラムは、シンボリックな表現（S式）で構成され、
    ; これは次のように見えます
    (some-function args)
    ; さて、代表的な「Hello World」の例です
    (print "hello world")
    
    ;; 単純なデータ型
    ; 全ての単純なデータ型は、Pythonの対応するものと同じです
    42 ; => 42
    3.14 ; => 3.14
    True ; => True
    4+10j ; => (4+10j) 複素数
    
    ; それでは、簡単な算術演算から始めましょう
    (+ 4 1) ;=> 5
    ; 演算子は、他のLispと同様に、全ての引数に適用されます
    (+ 4 1 2 3) ;=> 10
    (- 2 1) ;=> 1
    (* 4 2) ;=> 8
    (/ 4 1) ;=> 4
    (% 4 2) ;=> 0 剰余演算子
    ; べき乗は、Pythonと同様に ** 演算子で表されます
    (** 3 2) ;=> 9
    ; 入れ子の式も期待通りに動作します
    (+ 2 (* 4 2)) ;=> 10
    ; 論理演算子や比較演算子も同様に動作します
    (= 5 4) ;=> False
    (not (= 5 4)) ;=> True
    
    ;; 変数
    ; 変数は setv を使って設定されます。変数名には ()[]{}",'`;#| を除き
    ; UTF-8が使用可能です
    (setv a 42)
    (setv π 3.14159)
    (def *foo* 42)
    ;; 他のコンテナデータ型
    ; 文字列、リスト、タプル、辞書
    ; これらは、Pythonのコンテナ型と全く同じです
    "hello world" ;=> "hello world"
    ; 文字列操作はPythonと同様に機能します
    (+ "hello " "world") ;=> "hello world"
    ; リストは [] で作成され、インデックスは 0 から始まります
    (setv mylist [1 2 3 4])
    ; タプルは変更不可なデータ構造です
    (setv mytuple (, 1 2))
    ; 辞書はキーと値のペアです
    (setv dict1 {"key1" 42 "key2" 21})
    ; :name を使ってキーワードを定義でき、辞書のキーとして使用できます
    (setv dict2 {:key1 41 :key2 20})
    ; `get` を使って、インデックス/キーの要素を取得します
    (get mylist 1) ;=> 2
    (get dict1 "key1") ;=> 42
    ; キーワードが使用されている場合は、それを直接呼び出せます
    (:key1 dict2) ;=> 41
    
    ;; 関数とその他のプログラム構造
    ; 関数は defn を使って定義され、最後のS式がデフォルトで返されます
    (defn greet [name]
      "簡単な挨拶" ; オプションのドキュメント文字列
      (print "hello " name))
    
    (greet "bilbo") ;=> "hello bilbo"
    
    ; 関数はオプション引数やキーワード引数を取ることができます
    (defn foolists [arg1 &optional [arg2 2]]
      [arg1 arg2])
    
    (foolists 3) ;=> [3 2]
    (foolists 10 3) ;=> [10 3]
    
    ; さらに、可変長引数やキーワード引数を使用することもできます
    (defn something-fancy [wow &rest descriptions &kwargs props]
      (print "Look at" wow)
      (print "It's" descriptions)
      (print "And it also has:" props))
    
    (something-fancy "My horse" "amazing" :mane "spectacular")
    
    ; `apply` を使って、スプラット演算子の代わりに引数を適用します
    (apply something-fancy ["My horse" "amazing"] { "mane" "spectacular" })
    
    ; 無名関数は `fn` または `lambda` を使って作成され、`defn`と似ています
    (map (fn [x] (* x x)) [1 2 3 4]) ;=> [1 4 9 16]
    
    ;; シーケンス操作
    ; Hyには、シーケンス操作用の組み込みユーティリティがあります
    ; `first` または `car` を使って、最初の要素を取得します
    (setv mylist [1 2 3 4])
    (setv mydict {"a" 1 "b" 2})
    (first mylist) ;=> 1
    
    ; `cut` を使ってリストをスライスします
    (cut mylist 1 3) ;=> [2 3]
    
    ; `get` を使って、リストまたは辞書から要素を取得します
    (get mylist 1) ;=> 2
    (get mydict "b") ;=> 2
    ; リストのインデックスはPythonと同様に0から始まります
    ; `assoc` を使ってキー/インデックスに要素を設定します
    (assoc mylist 2 10) ; mylistを [1 2 10 4] にします
    (assoc mydict "c" 3) ; mydictを {"a" 1 "b" 2 "c" 3} にします
    ; シーケンス操作を楽しくする他のコア関数もたくさんあります
    
    ;; Pythonとの相互運用
    ;; `import` はPythonと同様に機能します
    (import datetime)
    (import functools [partial reduce]) ; functoolsからpartialとreduceをインポート
    (import matplotlib.pyplot :as plt) ; fooをbarとしてインポート
    ; すべての組み込みPythonメソッドなどはHyからアクセス可能です
    ; a.foo(arg) は (.foo a arg) のように呼び出します
    (.split (.strip "hello world  ")) ;=> ["hello" "world"]
    
    ; "スレッドマクロ" と呼ばれる、値に対して複数の関数を実行するための
    ; ショートカットがあります。これは矢印で示されます:
    (-> "hello world  " (.strip) (.split)) ;=> ["hello" "world]
    ; 矢印は、呼び出しごとに値を最初の引数として渡します。例えば:
    (-> 4 (* 3) (+ 2))
    ; は次のようになります:
    (+ (* 4 3) 2)
    
    ; 値を2番目の引数として渡す "スレッドテールマクロ" もあります。比較してみてください:
    (-> 4 (- 2) (+ 1)) ;=> 3
    (+ (- 4 2) 1) ;=> 3
    ; これに対して:
    (->> 4 (- 2) (+ 1)) ;=> -1
    (+ 1 (- 2 4)) ;=> -1
    
    ;; 条件分岐
    ; (if condition (trueの場合の処理) (falseの場合の処理))
    (if (= passcode "moria")
      (print "welcome")
      (print "Speak friend, and Enter!"))
    
    ; 複数のifやelse ifの条件を `cond` でグループ化します
    (cond
      (= someval 42) (print "Life, universe and everything else!")
      (> someval 42) (print "val too large")
      (< someval 42) (print "val too small"))
    
    ; `do` でステートメントをグループ化し、これらは順次実行されます
    ; `defn` などのフォームには暗黙の `do` が含まれます
    (do
      (setv someval 10)
      (print "someval is set to " someval)) ;=> 10
    
    ; `let` でレキシカルバインディングを作成し、これらで定義された変数は
    ; ローカルスコープを持ちます
    (let [nemesis {"superman" "lex luther"
                    "sherlock" "moriarty"
                    "seinfeld" "newman"}]
      (for [[h v] (.items nemesis)]
        (print (.format "{0}'s nemesis was {1}" h v))))
    
    ;; クラス
    ; クラスは次のように定義されます
    (defclass Wizard [object]
      (defn __init__ [self spell]
        (setv self.spell spell))
    
      (defn get-spell [self]
        self.spell))
    ```



```python

```

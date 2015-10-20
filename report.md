課題
プロファイラでボトルネックを探す

実施内容
2通り試した．
１）profileライブラリを用いてプロファイルを行った．
まず，ruby -r profile ./bin/trema.run ./lib.cbench.rb　でcbench.rbを実行．
次にcbenchを動かし，その後cbench.rbを終了するとプロファイルが出力された．
２）ruby-profでも試した．これは
ruby-prof ./bin/trema.run ./lib.cbench.rbを実行し，その後cbench.rbを終了するとプロファイルされた．

今回は２）の方法でボトルネックを探す．
ruby-profでのプロファイルの結果を以下に示す．


%self      total      self      wait     child     calls  name
  6.43      0.045     0.045     0.000     0.000    19112   Symbol#to_s
  2.88      0.232     0.020     0.000     0.212     6740  *Array#each
  2.56      0.018     0.018     0.000     0.000     7083   Array#initialize_copy
  2.49      0.677     0.017     0.000     0.659      595  *Kernel#require
  2.45      0.017     0.017     0.000     0.000    16068   Kernel#instance_variable_set
  2.24      0.052     0.016     0.000     0.036    12192   Kernel#dup
  2.02      0.036     0.014     0.000     0.022    12268   Kernel#initialize_dup
  1.99      0.017     0.014     0.000     0.003     2281  *BinData::DSLMixin::DSLParser#endian
  1.90      0.050     0.013     0.000     0.037      436  *Module#module_eval
  1.64      0.013     0.011     0.000     0.002      184   BinData::DSLMixin::DSLParser#initialize
  1.25      0.009     0.009     0.000     0.000     3404   Gem::Dependency#type
  1.25      0.277     0.009     0.000     0.268     5893  *Class#new
  0.97      0.072     0.007     0.000     0.065        4   Bundler::SpecSet#for
  0.93      0.006     0.006     0.000     0.000     6358   Symbol#to_sym
  0.88      0.018     0.006     0.000     0.012      857   BinData::Registry#underscore_name
  0.76      0.005     0.005     0.000     0.000    10700   Gem::Specification#default_value
  0.75      0.005     0.005     0.000     0.000      616   IO#set_encoding
  0.72      0.024     0.005     0.000     0.019      274   Forwardable#def_instance_delegator
  0.62      0.010     0.004     0.000     0.006     1961  *BinData::AcceptedParametersPlugin#accepted_parameters
  0.59      0.005     0.004     0.000     0.001      150   Module#class_eval
  0.55      0.004     0.004     0.000     0.000     1762   String#gsub
  0.52      0.012     0.004     0.000     0.009      825   BinData::Registry#is_registered?
  0.49      0.003     0.003     0.000     0.000      973   String#sub
  0.48      0.004     0.003     0.000     0.000      535   Bundler::StubSpecification#_remote_specification
  0.46      0.003     0.003     0.000     0.000     3413   BinData::DSLMixin::DSLParser#parser_abilities
  0.45      0.013     0.003     0.000     0.010     1414   Array#map
  0.42      0.003     0.003     0.000     0.000     3183   String#initialize_copy
  0.41      0.006     0.003     0.000     0.003     1704  *Hash#each
  0.41      0.003     0.003     0.000     0.000     2866   Module#method_added
  0.41      0.003     0.003     0.000     0.000     1594   Regexp#=~
  0.40      0.003     0.003     0.000     0.000     4970   Kernel#hash
  0.37      0.003     0.003     0.000     0.000     6772   Kernel#nil?
  0.36      0.003     0.003     0.000     0.000      292   Kernel#define_singleton_method
  0.35      0.008     0.002     0.000     0.006     2973   BinData::DSLMixin::DSLParser#option?
  0.33      0.048     0.002     0.000     0.045      771  *BinData::SanitizedParameters#initialize
  0.32      0.002     0.002     0.000     0.000     4460   Array#include?
  0.30      0.002     0.002     0.000     0.000     3196   BasicObject#!
  0.30      0.002     0.002     0.000     0.000     1508   String#to_s
  0.30      0.005     0.002     0.000     0.002      766   Bundler::SpecSet#lookup
  0.29      0.002     0.002     0.000     0.000     1719   Hash#[]=
  0.29      0.003     0.002     0.000     0.001      953  *<Class::BinData::Base>#arg_processor

・・・・・
(以下省略)

この結果は関数毎にself timeに関して降順になっている．ここで
  0.32      0.002     0.002     0.000     0.000     4460   Array#include? に着目した．
include?はArrayの要素に特定のものが含まれているかどうかを調べるために使われるもので，対象となる配列のサイズが大きい場合その分時間がかかってしまう．
selftimが0.002となっているが呼び出し回数が多いので，場合によってボトルネックとなってしまう．

課題
プロファイラでボトルネックを探す

実施内容
2通り試した．
１）profileライブラリを用いてプロファイルを行った．
まず，ruby -r profile ./bin/trema.run ./lib.cbench.rb　でcbench.rbを実行．
次にcbenchを動かし，その後cbench.rbを終了するとプロファイルが出力された．
２）ruby-profでも試した．これは
ruby-prof ./bin/trema.run ./lib.cbench.rbを実行し，その後cbench.rbを終了するとプロファイルされた．
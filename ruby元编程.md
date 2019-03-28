# 元编程

**ruby最擅长解决的是元编程 **

>元编程：能写程序的程序，程序可以根据他应用时的状态而改变

* 开放类
    * 能对现有的类添加方法，包括NilClass，String可以轻松进行重定义，判断字符串是否为空等。
* method_missing
    * 很强大的功能，可以实现一些有趣行为
* 模块

``` ruby
    module ActsAsCsv
# 当ActAsCsv被include此方法被调用
  def self.included(base)
    base.extend ClassMethods
  end

  module ClassMethods
    def act_as_csv
      include InstanceMethods
    end
  end
# 实例方法为实例写入方法
  module InstanceMethods

    def read
      @csv_contents = []
      filename = self.class.to_s.downcase + '.txt'
      file = File.new(filename)
      @headers = file.gets.chomp.split(', ')

      file.each do |row|
        @csv_contents <<row.chomp.split(', ')
      end
    end

    def each
      yield RubyCsv.new
      @csv_contents
    end

    def method_missing name,*args
      i = 0
      save_num = @headers[i].chomp.split(',')
      while save_num[i]!= name.to_s do
        @i += 1
      end
      # puts @csv_contents[0][0]
      save_content=[]
      @csv_contents.each do |content|
        save_content << content[0].chomp.split(',')[i]
      end
      save_content
    end


# 定义这两个元素的get和set方式
  attr_accessor :headers, :csv_contents

    def initialize
      read
    end
  end

end

class RubyCsv
  include ActsAsCsv
  act_as_csv

end

m = RubyCsv.new
# puts m.headers.inspect
# puts m.csv_contents.inspect
m.each{|row| puts row.one }
```
> 模块是ruby最流行的元编程模式

> extend将作为类的方法，而include则作为实例的方法

> 这种做法看起来很复杂，但不同于继承，他很简洁易懂，实现了一个会写程序的程序。

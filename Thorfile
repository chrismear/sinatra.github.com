require "rubygems"
require "haml"
require "rdoc/markup/to_html"

class Legend < Thor
  desc "build", "Build Sinatra website"
  def build
    build_index
    build_book
    build_api
  end

  private
    def build_index
      write_file "index.html", "Sinatra", readme
    end

    def build_book
      write_file "book.html", "Sinatra Book", book
    end

    def readme
      fetch_sinatra

      RDoc::Markup::ToHtml.new.convert(File.read("_sinatra/README.rdoc"))
    end

    def book
      system "git clone git://github.com/sinatra/sinatra-book.git _book" unless File.directory?("_book")
      system "cd _book && git pull &>/dev/null && thor book:build"

      File.read("_book/output/sinatra-book.html")[/<body>(.*?)<\/body>/m, 1]
    end

    def build_api
      fetch_sinatra

      Dir.chdir "_sinatra" do
        system(<<-EOF)
          hanna --charset utf8 \
            --fmt html \
            --inline-source \
            --line-numbers \
            --main README.rdoc \
            --op ../api \
            --title 'Sinatra API Documentation' \
            lib/**/*.rb README.rdoc
        EOF
      end
    end

    def fetch_sinatra
      system "git clone git://github.com/sinatra/sinatra.git _sinatra" unless File.directory?("_sinatra")
      system "cd _sinatra && git pull &>/dev/null"
    end

    def write_file(file_name, title, content)
      template = File.read("_template.haml")

      haml   = Haml::Engine.new(template, :format => :html5, :attr_wrapper => '"')
      result = haml.render(Object.new, {:title => title, :content => content})

      File.open(file_name, "w") { |f| f << result }
    end
end
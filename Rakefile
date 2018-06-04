require 'webrick'
task default: %w[exit]

desc "Exit"
task :exit do
  exit 0
end

desc "Run server"
task :server do
  Dir.chdir('app') do
    mime_types = WEBrick::HTTPUtils::DefaultMimeTypes
    mime_types.store('js', 'application/javascript')
    mime_types.store('json', 'application/json')

    WEBrick::HTTPServer.new(
      :Port => 9090,
      :DocumentRoot => '.',
      :MimeTypes => mime_types).start
  end
end

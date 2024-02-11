```ruby
require 'json'

def flatten_keys(data, parent_key = nil)
  flattened_keys = {}

  data.each_with_index do |value, index|
    current_key = parent_key ? "#{parent_key}.#{index}" : index.to_s

    if value.is_a?(Hash)
      flattened_keys.merge!(flatten_hash(value, current_key))
    elsif value.is_a?(Array)
      flattened_keys.merge!(flatten_array(value, current_key))
    else
      flattened_keys[current_key] = value
    end
  end

  flattened_keys
end

def flatten_hash(hash, parent_key = nil)
  flattened_keys = {}

  hash.each do |key, value|
    current_key = parent_key ? "#{parent_key}.#{key}" : key.to_s

    if value.is_a?(Hash)
      flattened_keys.merge!(flatten_hash(value, current_key))
    elsif value.is_a?(Array)
      flattened_keys.merge!(flatten_array(value, current_key))
    else
      flattened_keys[current_key] = value
    end
  end

  flattened_keys
end

def flatten_array(array, parent_key = nil)
  flattened_keys = {}

  array.each_with_index do |value, index|
    current_key = parent_key ? "#{parent_key}.#{index}" : index.to_s

    if value.is_a?(Hash)
      flattened_keys.merge!(flatten_hash(value, current_key))
    elsif value.is_a?(Array)
      flattened_keys.merge!(flatten_array(value, current_key))
    else
      flattened_keys[current_key] = value
    end
  end

  flattened_keys
end

# Example JSON data with a nested structure
json_data = <<~JSON
  {
    "hash_key": {
      "sub_hash_key": {
        "key1": "value1",
        "key2": "value2"
      },
      "array_key": [
        {
          "key3": "value3",
          "key4": "value4"
        },
        {
          "key5": "value5",
          "key6": "value6"
        }
      ]
    },
    "array": [
      "value7",
      "value8",
      ["value9", "value10"],
      {
        "key7": "value11",
        "key8": "value12"
      }
    ]
  }
JSON

# Parse JSON data
parsed_data = JSON.parse(json_data)

# Flatten keys
flattened_keys = flatten_hash(parsed_data)

# Output flattened keys
flattened_keys.each do |key, value|
  puts "#{key}: #{value}"
end

def resume_structure(flattened_data)
  nested_data = {}

  flattened_data.each do |key, value|
    keys = key.split('.')
    current_hash = nested_data

    keys.each_with_index do |key_part, index|
      if index == keys.length - 1
        if numeric_key?(key_part) && !is_array_index?(keys[0..index-1].join('.'))
          current_hash[key_part.to_i] = value
        else
          current_hash[key_part] = value
        end
      else
        current_hash[key_part] ||= {}
        current_hash = current_hash[key_part]
      end
    end
  end

  nested_data
end

def numeric_key?(key)
  key.match?(/^\d+$/)
end

def is_array_index?(key_prefix)
  key_prefix.end_with?('.')
end

# Example flattened data
flattened_data = {
  "hash_key.sub_hash_key.key1" => "value1",
  "hash_key.sub_hash_key.key2" => "value2",
  "hash_key.array_key.0.key3" => "value3",
  "hash_key.array_key.0.key4" => "value4",
  "hash_key.array_key.1.key5" => "value5",
  "hash_key.array_key.1.key6" => "value6",
  "array.0" => "value7",
  "array.1" => "value8",
  "array.2.0" => "value9",
  "array.2.1" => "value10",
  "array.3.key7" => "value11",
  "array.3.key8" => "value12"
}

# Resume structure
resumed_data = resume_structure(flattened_data)

# Output resumed structure
puts JSON.pretty_generate(resumed_data)

```

```ruby

require 'yaml'

def flatten_yaml_keys(data, parent_key = nil)
  flattened_keys = {}

  data.each do |key, value|
    current_key = parent_key ? "#{parent_key}.#{key}" : key.to_s

    if value.is_a?(Hash)
      flattened_keys.merge!(flatten_keys(value, current_key))
    else
      flattened_keys[current_key] = value
    end
  end

  flattened_keys
end

# Example YAML data
yaml_data = <<~YAML
  section:
    subsection:
      key1: value1
      key2: value2
      array_key:
        - value3
        - value4
YAML

# Parse YAML data
parsed_data = YAML.load(yaml_data)

# Flatten keys
flattened_keys = flatten_yaml_keys(parsed_data)

# Output flattened keys
flattened_keys.each do |key, value|
  puts "#{key}: #{value}"
end

```

```ruby
def deep_equal?(hash1, hash2)
  return true if hash1 == hash2

  return false unless hash1.is_a?(Hash) && hash2.is_a?(Hash)
  return false unless hash1.keys.sort == hash2.keys.sort

  hash1.keys.all? { |key| deep_equal?(hash1[key], hash2[key]) }
end

hash1 = { a: 1, b: { c: 2, d: { e: 3 } } }
hash2 = { a: 1, b: { c: 2, d: { e: 3 } } }

if deep_equal?(hash1, hash2)
  puts "The nested hashes are identical."
else
  puts "The nested hashes are different."
end
```
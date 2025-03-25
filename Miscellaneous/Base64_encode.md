To differentiate between strict and non-strict Base64 encoded strings using regex, consider the following patterns:


---

1. Strict Base64 Regex

A strict Base64-encoded string must:

Be composed of valid Base64 characters (A-Z, a-z, 0-9, +, /).

Have a length that is a multiple of 4.

End with either 0, 1, or 2 padding (= characters).


Regex for Strict Base64:

\A(?:[A-Za-z0-9+/]{4})*(?:[A-Za-z0-9+/]{4}|[A-Za-z0-9+/]{3}=|[A-Za-z0-9+/]{2}==)\z

Explanation:

1. (?:[A-Za-z0-9+/]{4})* → Matches complete blocks of 4 valid characters.


2. (?:[A-Za-z0-9+/]{4}|[A-Za-z0-9+/]{3}=|[A-Za-z0-9+/]{2}==) → Ensures that the last block follows strict padding rules.


3. \A...\z → Enforces matching the entire string (beginning to end).



Examples:

✅ Valid Strict Base64:

"U29tZSBzdHJpbmc="

"VGhpcyBpcyBzdHJpY3Q="

"U3RyaWN0QmFzZTY0VGVzdA=="


❌ Invalid Strict Base64:

"SGVsbG8h" (length not a multiple of 4)

"U29tZSBzdHJpbmc==" (extra = padding)



---

2. Non-Strict (Lenient) Base64 Regex

A non-strict Base64-encoded string:

Can have any length (not necessarily a multiple of 4).

May omit padding (=).

Still consists of only Base64 characters (A-Z, a-z, 0-9, +, /).


Regex for Non-Strict Base64:

\A[A-Za-z0-9+/]+={0,2}\z

Explanation:

1. [A-Za-z0-9+/]+ → Allows any number of valid Base64 characters.


2. ={0,2} → Allows up to two padding = characters at the end.


3. \A...\z → Ensures it matches the entire string.



Examples:

✅ Valid Non-Strict Base64:

"U29tZSBzdHJpbmc" (No padding)

"SGVsbG8" (Non-multiple-of-4 length)

"QmFzZTY0VGVzdD0=" (Single = padding)


❌ Invalid Non-Strict Base64:

"Invalid*Base64==" (Invalid character *)

"12345" (Too short and doesn't match the Base64 character set)



---

Conclusion

Use the strict regex to ensure compliance with Base64 encoding rules.

Use the non-strict regex for lenient validation, allowing missing padding.


Would you like a Ruby function to implement this validation?

No, in non-strict mode, the string length does not need to be divisible by 4.

Key Differences:

Why?

1. Strict Mode:

Base64 encoding always generates output in multiples of 4.

If the input data isn't a multiple of 3 bytes, padding (=) is used to ensure a multiple of 4.



2. Non-Strict Mode:

Some Base64 implementations omit padding (=), making the length not necessarily a multiple of 4.

Some Base64 variations (e.g., URL-safe encoding) don't require padding at all.




Examples

✅ Valid Non-Strict Base64 Strings (Not Divisible by 4)

"U29tZQ" → ("Some", missing padding)

"SGVsbG8" → ("Hello", missing padding)

"QmFzZTY0VGVzdA" → ("Base64Test", missing padding)


❌ Invalid Non-Strict Base64 (Wrong Characters)

"Invalid*Base64" → (* is not a Base64 character)

"HelloWorld!" → (Not Base64 encoded)



---

Ruby Function to Validate Non-Strict Base64

def non_strict_base64?(str)
  !!(str =~ /\A[A-Za-z0-9+\/]+={0,2}\z/)
end

puts non_strict_base64?("U29tZQ")  # true (valid non-strict Base64)
puts non_strict_base64?("SGVsbG8") # true (valid non-strict Base64)
puts non_strict_base64?("Invalid*Base64") # false (invalid character `*`)

Would you like a function to decode and verify non-strict Base64 in Ruby?


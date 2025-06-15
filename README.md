# reimagined-Rotor-random-seed-password-system
# This project implements a custom rotor cipher, inspired by historical encryption machines like the Enigma, but designed with modern concepts such as dynamic rotor shifts and seed-based key generation.
import string
import random
import hashlib

# Supported characters
LETTERS = string.ascii_letters + string.digits + " .,!?()"
ROTOR_CHARS = "abcd"  # 4 rotor characters

# Get offset from rotor character (abcd → 0–3)
def rotor_char_offset(ch):
    return ROTOR_CHARS.index(ch)

# Shift character by offset
def shift_char(ch, offset):
    if ch not in LETTERS:
        return ch
    idx = LETTERS.index(ch)
    return LETTERS[(idx + offset) % len(LETTERS)]

# Generate dynamic offset (combine seed + rotor)
def dynamic_offset(i, aux_key, seed):
    base_offset = rotor_char_offset(aux_key[i % len(aux_key)])
    
    # Create hash-based random number from seed and position
    seed_input = f"{seed}:{i}"
    hash_digest = hashlib.sha256(seed_input.encode()).hexdigest()
    
    # Take first two hex digits, convert to int
    prng_value = int(hash_digest[:2], 16) % len(ROTOR_CHARS)
    
    return (base_offset + prng_value) % len(LETTERS)

# Encrypt function
def encrypt(text, aux_key, seed):
    encrypted = ""
    for i, ch in enumerate(text):
        offset = dynamic_offset(i, aux_key, seed)
        encrypted += shift_char(ch, offset)
    return encrypted

# Decrypt function
def decrypt(cipher, aux_key, seed):
    decrypted = ""
    for i, ch in enumerate(cipher):
        offset = dynamic_offset(i, aux_key, seed)
        decrypted += shift_char(ch, -offset)
    return decrypted

# Main program
def main():
    print("=== 🚀 Dynamic Rotor Cipher Machine (Seed-Driven) ===")
    mode = input("Select mode (e = encrypt, d = decrypt): ").strip().lower()
    
    if mode == "e":
        text = input("Enter plaintext: ")
        aux_key = input("Enter 32-character rotor key (only 'a' to 'd'): ")
        seed = input("Enter random seed (any string): ")
        cipher = encrypt(text, aux_key, seed)
        print("🔐 Encrypted text: ", cipher)
    
    elif mode == "d":
        cipher = input("Enter ciphertext: ")
        aux_key = input("Enter 32-character rotor key (only 'a' to 'd'): ")
        seed = input("Enter random seed (must match encryption): ")
        plain = decrypt(cipher, aux_key, seed)
        print("🔓 Decrypted text: ", plain)

    else:
        print("Invalid mode, please enter 'e' or 'd'.")

if __name__ == "__main__":
    main()

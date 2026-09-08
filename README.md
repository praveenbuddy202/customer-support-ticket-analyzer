"""
Customer Support Ticket Analyzer
---------------------------------
A Python-based system that stores, cleans, analyses, and extracts
insights from customer support tickets.
"""

import string

# ------------------------------------------------------------------
# STEP 1: Preloaded Tickets
# ------------------------------------------------------------------
ticket_data = {
    'Ticket_No': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    'Customer_Name': [
        'Ravi', 'Meera', 'Sam', 'Anu', 'Rakesh',
        'Divya', 'Arjun', 'Kiran', 'Leela', 'Nisha'
    ],
    'Issue_Description': [
        ' Internet not working!!! ',
        'slow response, very poor service ',
        'GREAT support! issue resolved.',
        ' okay... need help ',
        'not BAD but slow',
        'Excellent guidance, Very Helpful!',
        'good support and good behaviour!',
        'Poor handling of technical issue',
        'Satisfied. Could be better.',
        'Good service... quick response.'
    ],
    'Priority': [
        'High', 'Low', 'High', 'Medium', 'Low',
        'High', 'Medium', 'High', 'Low', 'Medium'
    ]
}


def print_tickets(data, title="Ticket Data"):
    """Print ticket_data dictionary of lists in a readable, tabular way."""
    print(f"\n{'='*70}\n{title}\n{'='*70}")
    header = f"{'No.':<5}{'Name':<12}{'Priority':<10}{'Issue Description'}"
    print(header)
    print('-' * 70)
    for i in range(len(data['Ticket_No'])):
        print(f"{data['Ticket_No'][i]:<5}"
              f"{data['Customer_Name'][i]:<12}"
              f"{data['Priority'][i]:<10}"
              f"{data['Issue_Description'][i]}")
    print('=' * 70)


print_tickets(ticket_data, "Initial Ticket Data")


# ------------------------------------------------------------------
# STEP 2: Add More Tickets
# ------------------------------------------------------------------
def get_valid_priority():
    """Prompt until a valid priority (High/Medium/Low) is entered."""
    valid = {'high': 'High', 'medium': 'Medium', 'low': 'Low'}
    while True:
        p = input("Priority (High/Medium/Low): ").strip().lower()
        if p in valid:
            return valid[p]
        print("Invalid priority. Please enter High, Medium, or Low.")


def add_new_tickets(data):
    """Ask the user how many tickets to add, then collect and append them."""
    try:
        n = int(input("How many new tickets do you want to add? "))
    except ValueError:
        print("Invalid number entered. No tickets added.")
        return

    next_ticket_no = max(data['Ticket_No']) + 1 if data['Ticket_No'] else 1

    for i in range(n):
        print(f"\n--- New Ticket #{next_ticket_no} ---")
        name = input("Customer Name: ").strip()
        issue = input("Issue Description: ").strip()
        priority = get_valid_priority()

        data['Ticket_No'].append(next_ticket_no)
        data['Customer_Name'].append(name)
        data['Issue_Description'].append(issue)
        data['Priority'].append(priority)

        next_ticket_no += 1

    print(f"\n{n} ticket(s) added successfully.")


add_new_tickets(ticket_data)
print_tickets(ticket_data, "Ticket Data After Adding New Tickets")


# ------------------------------------------------------------------
# STEP 3: Text Cleaning for Issue Descriptions
# ------------------------------------------------------------------
SLANG_MAP = {
    'ok': 'okay',
    'okay': 'okay',
    'thx': 'thanks',
    'pls': 'please',
    'plz': 'please',
    'u': 'you',
    'gr8': 'great',
    'bad': 'bad',  # kept for clarity, no change needed
}


def clean_text(text):
    """
    Clean a single issue description:
    - remove punctuation
    - collapse multiple spaces into one
    - strip leading/trailing spaces
    - lowercase
    - replace slang/shorthand words
    """
    # Remove punctuation
    for punct in string.punctuation:
        text = text.replace(punct, '')

    # Lowercase
    text = text.lower()

    # Collapse multiple spaces and strip
    words = text.split()
    text = ' '.join(words).strip()

    # Replace slang/shorthand words
    words = text.split()
    words = [SLANG_MAP.get(w, w) for w in words]
    text = ' '.join(words)

    return text


def clean_all_descriptions(data):
    """Apply clean_text() to every issue description in place."""
    data['Issue_Description'] = [
        clean_text(desc) for desc in data['Issue_Description']
    ]


clean_all_descriptions(ticket_data)
print_tickets(ticket_data, "Ticket Data After Text Cleaning")


# ------------------------------------------------------------------
# STEP 4: Keyword-Based Issue Insights
# ------------------------------------------------------------------
def count_tickets_with_word(word):
    """
    Case-insensitive search.
    Returns how many ticket descriptions contain the given word.
    """
    word = word.lower()
    count = 0
    for desc in ticket_data['Issue_Description']:
        if word in desc.split():
            count += 1
    return count


print("\n" + "=" * 70)
print("Keyword-Based Issue Insights")
print("=" * 70)
for kw in ["poor", "good", "slow", "excellent"]:
    print(f"Tickets containing '{kw}': {count_tickets_with_word(kw)}")


# ------------------------------------------------------------------
# STEP 5: Final Summary & Insights
# ------------------------------------------------------------------

# 1. Display final cleaned ticket_data
print_tickets(ticket_data, "Final Cleaned Ticket Data")

# 2. Priority Analysis
high_count = ticket_data['Priority'].count('High')
medium_count = ticket_data['Priority'].count('Medium')
low_count = ticket_data['Priority'].count('Low')

print("\n" + "=" * 70)
print("Priority Analysis")
print("=" * 70)
print(f"High priority tickets   : {high_count}")
print(f"Medium priority tickets : {medium_count}")
print(f"Low priority tickets    : {low_count}")

# 3. Ticket with the Longest Issue Description (by word count)
word_counts = [len(desc.split()) for desc in ticket_data['Issue_Description']]
max_index = word_counts.index(max(word_counts))

print("\n" + "=" * 70)
print("Ticket With the Longest Issue Description")
print("=" * 70)
print(f"Ticket Number : {ticket_data['Ticket_No'][max_index]}")
print(f"Customer Name : {ticket_data['Customer_Name'][max_index]}")
print(f"Cleaned Issue : {ticket_data['Issue_Description'][max_index]}")
print(f"Word Count    : {word_counts[max_index]}")

# 4. Extract Unique Words Used
all_words = set()
for desc in ticket_data['Issue_Description']:
    all_words.update(desc.split())

sorted_words = sorted(all_words)

print("\n" + "=" * 70)
print("Unique Words Used Across All Issue Descriptions")
print("=" * 70)
print(f"Unique word count: {len(sorted_words)}")
print("Word list (sorted):")
print(sorted_words)

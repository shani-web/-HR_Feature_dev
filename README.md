# HR_Feature_dev

"""
Personalized Mock Test Generator
--------------------------------
This module generates a tailored mock test for each user based on their 
historical performance data, focusing more on weaker topics while keeping 
a balanced coverage for practice.
"""

import random
from typing import Dict, List

# ------------------------------
# Simulated Database / API Layer
# ------------------------------

def fetch_question_bank() -> Dict[str, List[int]]:
    """
    Simulates fetching questions from a tagged question bank.
    Returns:
        dict: {topic: [question_ids]}
    """
    return {
        "Arrays":   [101, 102, 103, 104, 105, 106, 107],
        "Strings":  [201, 202, 203, 204, 205, 206],
        "Graphs":   [301, 302, 303, 304, 305],
        "DP":       [401, 402, 403, 404, 405, 406],
        "Sorting":  [501, 502, 503, 504, 505]
    }

def fetch_user_performance(user_id: int) -> Dict[str, float]:
    """
    Simulates fetching user's topic-wise accuracy from past submissions.
    Args:
        user_id (int): Unique user identifier.
    Returns:
        dict: {topic: accuracy_score (0 to 1)}
    """
    # In real scenario, this will come from DB or analytics service
    return {
        "Arrays": 0.85,
        "Strings": 0.55,
        "Graphs": 0.40,
        "DP": 0.65,
        "Sorting": 0.92
    }

# ------------------------------
# Core Logic Layer
# ------------------------------

def generate_personalized_mock_test(user_id: int, total_questions: int = 15) -> List[int]:
    """
    Generates a personalized mock test for a given user.
    Args:
        user_id (int): Unique user identifier.
        total_questions (int): Number of questions in the mock test.
    Returns:
        list: List of selected question IDs.
    """
    question_bank = fetch_question_bank()
    performance = fetch_user_performance(user_id)

    # Sort topics by weakest first (lowest accuracy)
    sorted_topics = sorted(performance.items(), key=lambda x: x[1])

    selected_questions = []
    for topic, score in sorted_topics:
        # Allocate more questions for weaker topics
        topic_weight = max(1, int((1 - score) * total_questions / 2))
        selected = random.sample(question_bank[topic], min(topic_weight, len(question_bank[topic])))
        selected_questions.extend(selected)

        if len(selected_questions) >= total_questions:
            break

    # Fill remaining slots with random strong-topic questions
    while len(selected_questions) < total_questions:
        topic = random.choice(list(question_bank.keys()))
        q_id = random.choice(question_bank[topic])
        if q_id not in selected_questions:
            selected_questions.append(q_id)

    return selected_questions[:total_questions]

# ------------------------------
# Example Execution
# ------------------------------

if __name__ == "__main__":
    user_id = 12345  # Example user
    personalized_test = generate_personalized_mock_test(user_id, total_questions=15)

    print(f"Generated Personalized Mock Test for User {user_id}:")
    print(personalized_test)


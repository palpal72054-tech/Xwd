import requests

tokens = open("tokens.txt").read().splitlines()

print("""
1. Create Page
2. Like Post (from Pages)
3. Comment Post (from Pages)
""")

choice = input("Select: ")

# ------------------ PAGE CREATE ------------------
if choice == "1":
    name = input("Page name: ")

    for i, token in enumerate(tokens, start=1):
        r = requests.post(
            "https://graph.facebook.com/v19.0/me/accounts",
            data={
                "name": f"{name} {i}",
                "category": "Community",
                "access_token": token
            }
        )
        print(r.text)

# ------------------ LIKE POST ------------------
elif choice == "2":
    post_id = input("Post ID: ")

    for token in tokens:
        pages = requests.get(
            "https://graph.facebook.com/v19.0/me/accounts",
            params={"access_token": token}
        ).json()

        for p in pages.get("data", []):
            pt = p["access_token"]
            pid = p["id"]

            r = requests.post(
                f"https://graph.facebook.com/{post_id}/likes",
                data={"access_token": pt}
            )
            print("Liked from page:", pid)

# ------------------ COMMENT POST ------------------
elif choice == "3":
    post_id = input("Post ID: ")
    msg = input("Comment: ")

    for token in tokens:
        pages = requests.get(
            "https://graph.facebook.com/v19.0/me/accounts",
            params={"access_token": token}
        ).json()

        for p in pages.get("data", []):
            pt = p["access_token"]
            pid = p["id"]

            r = requests.post(
                f"https://graph.facebook.com/{post_id}/comments",
                data={
                    "message": msg,
                    "access_token": pt
                }
            )
            print("Commented from page:", pid)

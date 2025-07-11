import re

# Load log data from file
with open("mercier.sdsf.zos32.j4049.ptech.j90", "r") as file:
    log_data = file.read()

# Tokenize log into lines to preserve context
lines = log_data.splitlines()

cf_summary = {
    "available": [],
    "unavailable": []
}

cf_info = {}
current_cf = None

for line in lines:
    tokens = line.split()

    for i, token in enumerate(tokens):
        # If token looks like "CF2", "CF3", etc.
        if re.fullmatch(r"CF\d+", token):
            current_cf = token
            if current_cf not in cf_info:
                cf_info[current_cf] = {
                    "lines": []
                }

        if current_cf:
            cf_info[current_cf]["lines"].append(line)

# Now analyze each CF's lines
for cf, data in cf_info.items():
    all_text = " ".join(data["lines"]).upper()
    if "ALLOCATION NOT PERMITTED" in all_text or "MAINTENANCE MODE" in all_text:
        cf_summary["unavailable"].append(cf)
    else:
        cf_summary["available"].append(cf)

# Output the result
print("Available CFs:", cf_summary["available"])
print("Unavailable CFs:", cf_summary["unavailable"])

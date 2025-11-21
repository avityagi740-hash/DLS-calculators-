import math

# --------------------- DLS Resource Table ---------------------
DLS_TABLE = {
    50: [100, 95, 90, 85, 80, 75, 70, 65, 60, 55],
    40: [89, 82, 74, 66, 57, 47, 37, 27, 18, 9],
    30: [75, 67, 58, 49, 39, 30, 20, 11, 4, 1],
    20: [57, 48, 39, 29, 20, 12, 6, 2, 0, 0],
    10: [31, 23, 16, 10, 5, 2, 0, 0, 0, 0],
    0:  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
}

ANCHORS = [0, 10, 20, 30, 40, 50]

# --------------------- Helper Functions ---------------------

def find_anchors(overs):
    low = 0
    high = 50
    for a in ANCHORS:
        if a <= overs:
            low = a
        if a >= overs:
            high = a
            break
    return low, high

def get_resource(overs_left, wickets, max_overs):
    overs_50 = (overs_left / max_overs) * 50
    low, high = find_anchors(overs_50)
    if low == high:
        return DLS_TABLE[low][wickets]
    low_val = DLS_TABLE[low][wickets]
    high_val = DLS_TABLE[high][wickets]
    fraction = (overs_50 - low) / (high - low)
    return low_val + fraction * (high_val - low_val)

def calculate_target(team1_score, max_overs, team2_overs, team2_wickets=0):
    R1 = 100  # Team 1 played full allocation
    R2 = get_resource(team2_overs, team2_wickets, max_overs)
    par_score = (team1_score * R2) / R1
    target = math.floor(par_score) + 1
    return par_score, target

# --------------------- Main Menu ---------------------

def main():
    print("=== Menu-Driven Simple DLS Calculator ===")
    
    print("\nChoose the situation:")
    print("1. Rain interrupted FIRST innings")
    print("2. Rain interrupted SECOND innings")
    choice = input("Enter 1 or 2: ")
    
    max_overs = int(input("\nMatch overs per side: "))
    
    if choice == "1":
        # Rain in 1st innings
        team1_score = int(input("Team 1 runs before interruption: "))
        team1_overs = float(input("Team 1 overs played: "))
        team2_overs = float(input("Team 2 overs available after rain: "))
        team2_wickets = int(input("Team 2 wickets at start (usually 0): "))
        
        # Calculate adjusted target
        R1 = get_resource(team1_overs, 0, max_overs)  # Team 1 resources used
        R2 = get_resource(team2_overs, team2_wickets, max_overs)  # Team 2 resources
        par_score = (team1_score * R2) / R1
        target = math.floor(par_score) + 1

        print("\n--- Adjusted DLS Target ---")
        print(f"Team 1 resource used: {R1:.1f}%")
        print(f"Team 2 resource available: {R2:.1f}%")
        print(f"Par score for Team 2: {par_score:.2f}")
        print(f"Target for Team 2 to win: {target} runs")
        
    elif choice == "2":
        # Rain in 2nd innings
        team1_score = int(input("Team 1 final score: "))
        team2_overs = float(input("Team 2 overs available after rain: "))
        team2_wickets = int(input("Team 2 wickets at start (usually 0): "))
        
        par_score, target = calculate_target(team1_score, max_overs, team2_overs, team2_wickets)
        
        print("\n--- Adjusted DLS Target ---")
        print(f"Par score for Team 2: {par_score:.2f}")
        print(f"Target for Team 2 to win: {target+1} runs")
        
    else:
        print("Invalid choice! Please run again and select 1 or 2.")

# --------------------- Run ---------------------
if __name__ == "__main__":
    main()

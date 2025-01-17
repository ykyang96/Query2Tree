#!/usr/bin/env python3

def rename_fasta_headers(input_fasta, output_fasta):
    """
    Reads a FASTA file, renames duplicate headers by appending _2, _3, etc.,
    and writes the modified sequences to a new FASTA file.
    """
    header_counts = {}
    
    with open(input_fasta, 'r') as fin, open(output_fasta, 'w') as fout:
        for line in fin:
            if line.startswith(">"):
                # Remove trailing newline, keep header text
                original_header = line.strip()
                
                # If we have not seen this header before, initialize count
                if original_header not in header_counts:
                    header_counts[original_header] = 1
                    fout.write(original_header + "\n")
                else:
                    # We've seen this header before, so increment and rename
                    header_counts[original_header] += 1
                    new_header = f"{original_header}_{header_counts[original_header]}"
                    fout.write(new_header + "\n")
            else:
                # Write sequence lines as is
                fout.write(line)

if __name__ == "__main__":
    # Example usage:
    input_fasta = "/home/modifiedoutput.fasta"            # Update path as needed
    output_fasta = "/home/modifiedoutput_renamed.fasta"  # Output file
    rename_fasta_headers(input_fasta, output_fasta)

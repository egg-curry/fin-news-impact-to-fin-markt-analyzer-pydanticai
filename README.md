# fin-news-impact-to-fin-markt-analyzer-pydanticai
#This project aims to develop multi-agents via pydantic_ai to analyse the impact of financial news on the financial markets
import os
import logfire
logfire.configure()
logfire.instrument_pydantic_ai()
from pydantic import BaseModel
from pydantic_ai import Agent, RunContext
from pydantic_ai.usage import UsageLimits

agent = Agent('google-gla:gemini-2.0-flash')

news_agent = Agent(
    provider="google-gla",
    model="gemini-2.0-flash",
    system_prompt="""
        You are an experienced news analyst. Your role is to break down news headlines,
        summarize key developments, and flag socio-political implications on financial markets.
    """
)

market_agent = Agent(
    provider="google-gla",
    model="gemini-2.0-flash",
    system_prompt="""
        You are a financial markets expert. You job is to interpret the investor sentiment via stock market, 
        sector-wise performance, and macroeconomic indicators are being effected by current events or news cycles.
        .
    """
)

news_input_1= """
"headline": "Tesla crushes Q3 expectations with record profits, but Musk warns of 'turbulent t
"content": "Tesla (NASDAQ: TSLA) reported stunning Q3 results with earnings of $1.05 per share
"published_at": "2024-10-22T16:00:00Z"
"""
news_input_2= """
"headline": "Small biotech CureGen soars on FDA approval, analysts remain skeptical",
"content": "CureGen (NASDAQ: CURE), a small-cap biotech, received FDA approval for its novel c
"published_at": "2024-11-01T14:30:00Z"
"""
news_input_3= """
"headline": "Amazon announces 'transformational' AI venture, but at massive cost",
"content": "Amazon (NASDAQ: AMZN) unveiled Project Olympus, a $50 billion investment in AGI de
"published_at": "2024-09-15T09:00:00Z"
"""
news_input_4= """
"headline": "Regional bank FirstState posts record earnings amid industry turmoil",
"content": "FirstState Bank (NYSE: FSB) reported record Q2 earnings of $3.20 per share, up 45%
"published_at": "2024-10-12T10:30:00Z"
"""
news_input_5= """
"headline": "China tech giant ByteDance reports stellar growth, regulatory clouds remain",
"content": "ByteDance, TikTok's parent company, leaked financials show revenue grew 70% to $12
"published_at": "2024-11-21T18:45:00Z"
"""
news_summary = news_agent.run_sync(f"Analyze this news article: {news_input_1, news_input_2, news_input_3, news_input_4, news_input_5}")
print("\nNews Analyst says:\n", news_summary.output)

market_response = market_agent.run_sync(f"Given this article summary:\n'{news_summary.output}'\nHow might this impact financial markets?")
print("\nMarket Expert says:\n", market_response.output)

na_prefinal_response = news_agent.run_sync(
    f"The market expert responded:\n'{market_response.output}'\nDo you agree? Why not? Are there other concerns?"
)
print("\nFinal Response from News Analyst:\n", na_prefinal_response.output)

fe_prefinal_response = market_agent.run_sync(
    f"The news analyst responded:\n'{na_prefinal_response.output}'\nnDo you agree? Why not? Are there other concerns?"
)
print("\nFinal Response from News Analyst:\n", fe_prefinal_response.output)

na_final_response = news_agent.run_sync(
    f"The market expert responded:\n'{fe_prefinal_response.output}'\nDo you agree? Why not? Are there other concerns?"
)
print("\nFinal Response from News Analyst:\n", na_final_response.output)

fe_final_response = market_agent.run_sync(
    f"The news analyst responded:\n'{na_prefinal_response.output}'\nnDo you agree? Why not? Are there other concerns?"
)
print("\nFinal Response from News Analyst:\n", fe_final_response.output)
